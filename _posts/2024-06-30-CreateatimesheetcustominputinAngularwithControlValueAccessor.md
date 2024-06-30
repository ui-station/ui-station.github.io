---
title: "Angular ControlValueAccessor를 사용하여 커스텀 타임시트 입력 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-CreateatimesheetcustominputinAngularwithControlValueAccessor_0.png"
date: 2024-06-30 21:50
ogImage: 
  url: /assets/img/2024-06-30-CreateatimesheetcustominputinAngularwithControlValueAccessor_0.png
tag: Tech
originalTitle: "Create a timesheet custom input in Angular with ControlValueAccessor"
link: "https://medium.com/@lucadimolfetta/create-a-timesheet-custom-input-in-angular-with-controlvalueaccessor-7ba2800d2129"
---



![Image](/assets/img/2024-06-30-CreateatimesheetcustominputinAngularwithControlValueAccessor_0.png)

# 소개

Angular로 개발을 시작할 때, 매주 시간표를 작성하는 여러 입력 필드를 만들어야 하는 작업에서 어려움을 겪었습니다.

주요 문제는 흔히 쓰이는 HH:MM 형식의 시간표 항목들을 계산을 위한 소수점 숫자 형식으로 계속 변환해야 했다는 점이었습니다. 또한 백엔드에서 받은 소수점 숫자를 다시 HH:MM 형식으로 변환하여 페이지 로드 중에 항목을 채우는 역 문제도 해결해야 했습니다.


<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

처음에는 RxJs를 사용하여 문제를 해결했습니다. 그러나 이제 Angular 기능에 대해 더 자신감을 가지고 있으므로, 제 의견으로는 더 나은 캡슐화와 관심사 분리를 보장하는 다른 접근 방식을 시도해 보았습니다. 이 접근 방식은 ControlValueAccessor 인터페이스를 활용하여 사용자 정의 입력을 생성하고 사용자 정의 반응형 컨트롤을 만드는 것을 포함합니다.

# ControlValueAccessor: 개요 및 구현된 메서드

ControlValueAccessor 인터페이스는 사용자 정의 폼 컨트롤과 Angular의 반응형 폼 API간의 통신을 가능하게 하는 네 가지 메서드를 구현할 필요가 있습니다. 이러한 메서드는 다음과 같습니다:

- writeValue(newControlValue) → 이 메서드는 Angular 반응형 FormControl의 값이 변경될 때마다 호출됩니다. 그 목적은 모델에서 값이 업데이트되었음을 사용자 정의 컨트롤에 알리고, 뷰에 반영되어야 하는 경우에 호출됩니다. 예를 들어, writeValue() 메서드는 폼 컨트롤을 처음 초기화할 때 호출되거나(parentControl = new FormControl`number`(0, [Validators.required]) 또는 parentControl.setValue(4)와 같이 호출될 때 호출됩니다.
- registerOnChange(fn) → 저의 의견으로는, 이 인터페이스에서 가장 이해하기 어려운 메서드입니다. FormControl이 처음 생성될 때, registerOnChange(fn)이 인수(fn이라는 관례적인 이름)와 함께 호출됩니다. 이 인수는 뷰에서 모델로 값이 변경되었음을 알리기 위해 호출해야 하는 함수입니다. 명확하게 하기 위해, 대부분의 경우 사용자 정의 폼 컨트롤에서 다음 단계를 구현해야 합니다:

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 클래스 속성 내부에 fn 변수를 저장하여 나중에 사용자가 뷰의 입력 값을 업데이트할 때 호출할 수 있습니다.
- 사용자가 값을 변경할 때 중단하는 로직을 구현합니다.
- 이전에 저장한 클래스 속성에서 fn을 호출하여 새 값을 저장하도록 Angular FormControl에 통신합니다. 아래 예제를 참조하세요:

```js
export class MyCustomInput implements ControlValueAccessor {
//...other methods

//onChange는 사용자가 값을 업데이트할 때 저장할 클래스 속성입니다.
onChange!: (value: yourInputType) => void;

//단계 1
registerOnChange(fn) {
  this.onChange = fn;
}

//단계 2 (템플릿의 이벤트 바인딩)
onUserInput(newUserValue: T) {
  //단계 3
  this.onChange(newUserValue);
}
```

- registerOnTouched(fn) → registerOnChange()가 어떻게 작동하는지 이해하면, 이것도 아주 쉽습니다. 비슷하게 동작합니다: fn 콜백을 등록하고 클래스 속성에 저장한 다음, 부모 FormControl에 사용자 정의 컨트롤이 터치되었음을 통보하기 위해 호출합니다.
- setDisableState(isDisabled) → 이 메서드의 구현은 선택 사항입니다. 부모 FormControl의 상태가 DISABLED와 다른 상태 사이에서 변경될 때마다(INVALID일지라도 컨트롤이 활성화된 것을 기억하세요), 이 메서드가 호출됩니다. 인수 isDisabled는 true일 때 부모 컨트롤의 disable() 함수가 호출될 때, false일 때 enable()이 호출될 때로 설정된 부울입니다.

# ControlValueAccessor의 내 구현: 타임시트 입력

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이제 ControlValueAccessor가 어떻게 작동하는지 명확히 이해했으니 코드가 어떻게 동작하는지 확인해 봅시다. 제안 사항이 있으면 댓글로 알려주세요:

```js
//timehseet.component.ts
  implements ControlValueAccessor, OnDestroy, AfterViewInit
{
  readonly #timeEntryRegex = /[0-2]*[0-9]+:[0-5]{1}([0-9]{1})?/;
  #destroy: Subject<void> = new Subject();
  #injector = inject(Injector);
  #parentControl!: AbstractControl | null;

  invalidClasses = input<string[]>();

  innerControl = new FormControl<string>('', { updateOn: 'blur' });

  isInvalid = false;
  isDisabled = false;
  isTouched = false;

  onTouch: (() => void) | undefined;

  writeValue(timeEntry: number | null): void {
    this.innerControl.setValue(this.fromFloatToString(timeEntry));
  }

  registerOnChange(fn: any): void {
    this.innerControl.valueChanges
      .pipe(
        takeUntil(this.#destroy),
        tap(() => {
          this.isTouched = true;
          this.onTouch && this.onTouch();
        }),
        map((rawEntry) => this.convertEntry(rawEntry))
      )
      .subscribe((formattedEntry) => {
        this.innerControl.setValue(
          this.fromFloatToString(formattedEntry) || '',
          {
            emitEvent: false,
          }
        );

        fn(formattedEntry);
      });
  }

  registerOnTouched(fn: any): void {
    this.onTouch = fn;
  }

  setDisabledState(isDisabled: boolean): void {
    isDisabled ? this.innerControl.disable() : this.innerControl.enable();
  }

  markAsTouched() {
    this.isTouched = true;
    this.#parentControl?.updateValueAndValidity({
      onlySelf: true,
    });
    this.onTouch && this.onTouch();
  }

  ngOnDestroy(): void {
    this.#destroy.next();
    this.#destroy.complete();
  }

  ngAfterViewInit(): void {
    this.#parentControl = this.#injector.get(NgControl).control;

    this.#parentControl?.statusChanges
      .pipe(
        takeUntil(this.#destroy),
        filter(() => this.isTouched),
        map((status) => status === 'INVALID'),
        filter((isInvalid) => isInvalid !== this.isInvalid)
      )
      .subscribe((isInvalid: boolean) => {
        this.isInvalid = isInvalid;
      });
  }

  private convertEntry(rawEntry: string | null): number | null {
    //문자열을 숫자로 변환하는 메서드. 유효한 경우에만 변환합니다.
    //그렇지 않으면 null을 반환합니다.
  }

  private fromStringToFloat(timesheetEntry: string) {
    //....
  }

  private fromFloatToString(entryHours: number | null) {
   //...
  }
}
```

```js
<!-- timesheet.component.html-->
<input
  [ngClass]="isInvalid ? invalidClasses() : ''"
  type="text"
  class="form-control"
  (blur)="markAsTouched()"
  [formControl]="innerControl"
/>
```

# 메모

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

보시다시피, 저는 기본적으로 timesheet entry의 텍스트 형식을 처리하는 inner FormControl을 생성했습니다.

- 모델로부터 새로운 값이 도착하고 writeValue() 함수가 호출되면, fromFloatToString() 유틸리티 함수를 통해 값을 변환한 후 innerControl에 새 값을 설정합니다.
- 사용자가 timesheet entry를 입력하면, 모든 가능한 입력 형식을 고려하여 float로 변환을 시도하고 (유효한 경우 3, 3:00, 3.0), formattedEntry 변수를 생성합니다. 그런 다음 innerControl 값을 HH:MM 형식으로 새로 생성된 값으로 설정하고 (입력된 값이 유효하지 않은 경우 빈 문자열로), 동시에 fn(formattedEntry)를 호출하여 formattedEntry에 의해 생성된 실수 번호를 가진 parentControl의 값을 업데이트하도록 부모 컨트롤을 통지합니다.
- 코드에서 볼 수 있듯이, 이 경우에는 OnChange 함수를 저장하지 않습니다. 이는 구독 내부에서 매번 호출할 수 있기 때문입니다.
- customInput의 providers 배열에 다음 코드를 추가하는 것을 잊지 마세요:

```js
@Component({
  selector: 'app-timesheet-input',
  standalone: true,
  imports: [ReactiveFormsModule, NgClass],
  templateUrl: './timesheet-input.component.html',
  styleUrl: './timesheet-input.component.css',
  providers: [
    {
      provide: NG_VALUE_ACCESSOR,
      useExisting: forwardRef(() => TimesheetInputComponent),
      multi: true,
    },
  ],
})
```

이 코드를 추가하면 사용자 정의 구성 요소가 formControlName 지시문을 받도록 설정됩니다. 사용자 정의 formControl의 DI에 대한 자세한 정보는 이 리소스를 확인해주세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 최종 결과

프로젝트의 완전한 버전을 보고 싶다면, 제 StackBlitz를 확인해보세요.

# 감사의 말

이 간단하지만 중요한 프로젝트의 코드 가독성과 효율성을 향상시키는 데 도움을 주신 팔리오 비온디와 그의 텔레그램 커뮤니티에 특별히 감사드립니다.