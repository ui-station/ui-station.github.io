---
title: "Diffable Data Source를 사용한 다중 필터 컬렉션 뷰 생성 방법"
description: ""
coverImage: "/assets/img/2024-06-23-CreatingaMulti-FilterCollectionViewwithDiffableDataSource_0.png"
date: 2024-06-23 01:30
ogImage:
  url: /assets/img/2024-06-23-CreatingaMulti-FilterCollectionViewwithDiffableDataSource_0.png
tag: Tech
originalTitle: "Creating a Multi-Filter Collection View with Diffable Data Source"
link: "https://medium.com/justeattakeaway-tech/creating-a-multi-filter-collection-view-with-diffable-data-source-792b168907c4"
---

## iOS 프로덕션 코드에서 복잡한 UI 변경 사항을 확인하는 개념 증명

저스트 잇 테이크어웨이 닷컴의 확장으로 음식 뿐만 아니라 식료품 및 다양한 다른 제품도 제공하기 위해 카테고리 및 하위 카테고리에서 선택 및 세부 사항을 걸러내는 새로운 UI를 구현해야 했습니다.

![이미지](/assets/img/2024-06-23-CreatingaMulti-FilterCollectionViewwithDiffableDataSource_0.png)

요구 사항:

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

- 사용자는 첫 번째 캐로셀에서 하나의 카테고리만 선택할 수 있습니다. 항상 기본 카테고리가 선택됩니다.
- 카테고리를 선택하면 두 번째 캐로셀에 모든 가능한 옵션이 표시되며 사용자는 여러 옵션을 선택할 수 있습니다.
- 첫 번째와 두 번째 캐로셀에서 선택한 선택 사항을 반영하는 여러 섹션이 추가되어, 항목 간의 선택이 가능한 컬렉션 뷰가 만들어졌습니다.

제품 코드를 구현하는 데 큰 노력이 필요하여, 우리는 제품에 코드를 구현하기 전에 간단한 증명 프로젝트를 사용하기로 결정했습니다.

이 튜토리얼의 코드는 iOS에서 복잡한 사용자 인터페이스를 만들기 위한 증명 프로젝트로 사용되었습니다. 우리가 달성한 최종 목표는 그림에서 UI를 구현하는 것이었습니다.

# 증명 프로젝트의 중요성

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

개념 증명 (Proof of Concept, PoC)은 개발 과정에서 중요한 단계입니다. 이를 통해 개발자들은 아이디어와 구현의 실행 가능성을 전체 개발에 앞서 더 작은 규모로 테스트할 수 있습니다.

PoC를 만들면 잠재적인 문제를 빠르게 식별하고 가정을 검증하며 다양한 디자인 선택지를 탐색할 수 있습니다.

이 방법을 통해 우량하고 효과적인 해결책만을 추구하여 시간과 자원을 절약할 수 있습니다.

이 자습서에서 PoC는 최신 컬렉션 뷰 기술을 사용하여 복잡한 UI 요구사항을 관리하는 방법을 보여주며, 추가 개발을 위한 견고한 기반을 제공합니다.

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

현실 세계 시나리오에서는 식당 API를 사용하여 데이터를 가져와 컬렉션 뷰에 표시하지만, 이 튜토리얼에서는 개 API를 사용하여 개념을 설명하고 즐거운 시간을 가질 겁니다!

![이미지](https://miro.medium.com/v2/resize:fit:1200/1*96GQ1GHslcj5tvtG6uwAeg.gif)

이 튜토리얼에서는 UICollectionViewDiffableDataSource 및 UICollectionViewCompositionalLayout을 사용하여 여러 필터링 가능한 섹션을 포함하는 컬렉션 뷰를 만드는 방법을 안내합니다. 이 설정을 보여주기 위해 MultiFilterViewController 클래스를 사용할 것입니다.

# 단계 1: 데이터 모델 정의하기

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

데이터 모델을 정의하여 컬렉션 뷰에서 섹션과 항목을 나타낼 수 있도록 해보세요. 이 모델들은 Hashable 프로토콜을 준수해야 합니다. 이 예시에서는 세 가지 데이터 모델을 정의합니다: Category, Breed, Image. Content 구조체는 컬렉션 뷰에 표시될 섹션 유형과 항목을 정의합니다.

```swift
struct Category: Hashable {
    let name: String
    let range: ClosedRange<String>
    let isSelected: Bool
}

struct Breed: Hashable {
    let breed: String
    let apiKey: String
    let isSelected: Bool
}

struct Image: Hashable {
    let url: URL
}

struct Content {
    enum SectionType: Int, Hashable {
        case category
        case breed
        case images
    }

    struct Section: Hashable {
        var id: String
        var type: SectionType
    }

    enum Item: Hashable {
        case category(Category)
        case breed(Breed)
        case image(Image)

        func hash(into hasher: inout Hasher) {
            switch self {
            case .category(let item):
                hasher.combine(item.hashValue)
            case .breed(let item):
                hasher.combine(item.hashValue)
            case .image(let image):
                hasher.combine(image)
            }
        }
    }
}
```

우리는 세 가지 종류의 섹션을 가지고 있습니다: category, breed, images. 각 섹션에는 고유한 식별자와 유형이 있습니다. Item 열거형은 각 섹션 내 항목을 나타냅니다. hash(into:) 메서드는 각 항목이 고유하게 식별되도록 구현되어 있습니다.

# Step 2: 뷰 컨트롤러 설정하기

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

뷰 컨트롤러 MultiFilterViewController을 생성하고 해당 속성을 정의하세요.

```js
class MultiFilterViewController: UIViewController {

    static let sectionHeaderElementKind = "section-header-element-kind"

    var collectionView: UICollectionView!
    var collectionViewLayout: UICollectionViewCompositionalLayout!

    // 데이터를 가져오는 서비스
    let service: APIServing = Service()

    // 데이터를 관리하는 ViewModel
    let viewModel = SectionViewModel()

    var dataSource: UICollectionViewDiffableDataSource<Content.Section, Content.Item>?

    override func viewDidLoad() {
        super.viewDidLoad()
        updateInBackground()
    }

    init() {
        super.init(nibName: nil, bundle: nil)
        self.setupView()
        self.setupConstraints()
        self.dataSource = self.makeDataSource()
    }

    required init?(coder: NSCoder) {
        fatalError("구현되지 않았습니다")
    }
}
```

데이터를 가져오는 "service" 속성과 데이터를 관리하는 "viewModel" 속성을 정의했습니다. 또한 "dataSource" 속성을 사용하여 UICollectionViewDiffableDataSource를 통해 컬렉션 뷰 데이터 소스를 관리합니다. 컬렉션 뷰 레이아웃은 UICollectionViewCompositionalLayout을 사용하여 정의되었습니다.

# 단계 3: 뷰 및 제약 조건 설정

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

컬렉션 뷰를 초기화하고 구성하여 레이아웃을 설정하고 필요한 셀 및 보조 뷰를 등록합니다.

```js
extension MultiFilterViewController {
    func setupView() {
        collectionViewLayout = buildCompositionalLayout()
        collectionView = UICollectionView(frame: .zero, collectionViewLayout: collectionViewLayout)
        collectionView.translatesAutoresizingMaskIntoConstraints = false
        collectionView.allowsMultipleSelection = true
        collectionView.delegate = self
        view.backgroundColor = .white
        title = "Dog Breeds"
        view.addSubview(collectionView)
    }

    func setupConstraints() {
        NSLayoutConstraint.activate([
            collectionView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            collectionView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
            collectionView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            collectionView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor),
        ])
    }
}
```

이 단계에서는 컬렉션 뷰를 설정하고 뷰 컨트롤러의 뷰에 추가합니다. 또한 컬렉션 뷰가 전체 화면을 채우도록 제약 조건을 정의합니다.

# 단계 4: 셀 및 보조 뷰 등록하기

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

여러 항목 유형에 대한 셀 등록 및 섹션 헤더에 대한 보충 뷰 등록을 정의합니다.

```js
extension MultiFilterViewController {

    private func createLevelOneCellRegistration() -> UICollectionView.CellRegistration<LevelOneCollectionViewCell, Category> {
        UICollectionView.CellRegistration<LevelOneCollectionViewCell, Category> { [weak self] (cell, indexPath, item) in
            cell.item = item
            cell.icon = indexPath.row.isMultiple(of: 2) ? UIImage(systemName: "pawprint") : UIImage(systemName: "pawprint.fill")
            if item.isSelected {
                self?.collectionView.selectItem(at: indexPath, animated: false, scrollPosition: [])
            }
        }
    }

    private func createLevelTwoCellRegistration() -> UICollectionView.CellRegistration<LevelTwoCollectionViewCell, Breed> {
        UICollectionView.CellRegistration<LevelTwoCollectionViewCell, Breed> { [weak self] (cell, indexPath, item) in
            cell.item = item.breed
            Task { @MainActor in
                guard let self else { return }
                if let url = try await self.viewModel.randomImageURL(for: item, service: self.service) {
                    cell.image = try await ImageManager.shared.getImage(for: url)
                } else {
                    cell.image = UIImage(systemName: "pawprint")
                }
            }
            cell.position = indexPath.item
            if item.isSelected {
                self?.collectionView.selectItem(at: indexPath, animated: false, scrollPosition: [])
            }
        }
    }

    private func createCardCellRegistration() -> UICollectionView.CellRegistration<CardCollectionViewCell, Image> {
        UICollectionView.CellRegistration<CardCollectionViewCell, Image> { (cell, indexPath, item) in
            Task { @MainActor in
                cell.image = try await ImageManager.shared.getImage(for: item.url)
            }
        }
    }

    private func headerRegistration() -> UICollectionView.SupplementaryRegistration<SectionTitleView> {
        UICollectionView.SupplementaryRegistration
        <SectionTitleView>(elementKind: MultiFilterViewController.sectionHeaderElementKind) { [weak self] (supplementaryView, string, indexPath) in
            guard let section = self?.dataSource?.sectionIdentifier(for: indexPath.section) else { return }
            supplementaryView.label.text = section.id
            supplementaryView.backgroundColor = .white
        }
    }
}
```

이 단계에서는 Category, Breed 및 Image와 같은 다양한 항목 유형에 대한 셀 등록을 정의합니다. 또한 섹션 헤더에 대한 보충 뷰 등록을 정의합니다. 셀 등록은 적절한 데이터와 이미지로 셀을 구성합니다. 보충 뷰 등록은 섹션 식별자를 기반으로 섹션 제목을 설정합니다.

# 단계 5: 데이터 소스 구성

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

다음 단계에서는 `UICollectionViewDiffableDataSource`를 사용하여 diffable 데이터 소스를 생성합니다. 서로 다른 항목 유형에 대한 셀 프로바이더와 섹션 헤더를 위한 보충 뷰 프로바이더를 제공합니다. 셀 프로바이더는 항목 유형에 따라 적절한 셀을 대기열에서 꺼내옵니다. 그리고 보충 뷰 프로바이더는 섹션 헤더 뷰를 대기열에서 꺼내옵니다.

# 단계 6: Compositional Layout 구축

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

이번 단계에서는 컬렉션 뷰의 구성 레이아웃을 정의합니다. 섹션 제공자는 각 섹션의 레이아웃을 섹션 타입에 따라 반환합니다. 참고: dataSource를 사용하여 섹션 식별자를 검색하고 섹션 타입에 따라 레이아웃을 작성합니다.

# 단계 7: 데이터 가져오기 및 업데이트하기

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

뷰 모델에서 데이터를 가져와 diffable 데이터 소스의 snapshot을 업데이트하는 update() 메서드를 정의합니다.

```js
extension MultiFilterViewController {
    func update() async throws {
        let sections = try await viewModel.fetchData(service: service)
        var snapshot = NSDiffableDataSourceSnapshot<Content.Section, Content.Item>()
        let sectionKeys = sections.keys.sorted { section0, section1 in
            guard section0.type == section1.type else { return section0.type.rawValue < section1.type.rawValue }
            return section0.id < section1.id
        }
        for sectionKey in sectionKeys {
            if let items = sections[sectionKey] {
                snapshot.appendSections([sectionKey])
                snapshot.appendItems(items, toSection: sectionKey)
            }
        }
        dataSource?.apply(snapshot, animatingDifferences: true, completion: {
            print("Apply snapshot completed!")
        })
    }

    func updateInBackground() {
        Task {
            do {
                try await update()
            } catch {
                print(error)
            }
        }
    }
}
```

이 단계에서는 fetchData() 메서드를 사용하여 뷰 모델에서 데이터를 검색하고 섹션 및 항목으로 스냅샷을 만듭니다. 그런 다음 스냅샷을 데이터 소스에 적용하여 컬렉션 뷰를 업데이트합니다.

# 단계 8: 델리게이트 메서드를 사용한 선택 처리

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

UICollectionViewDelegate 메서드를 구현하여 항목 선택 및 선택 해제를 처리합니다.

```js
extension MultiFilterViewController: UICollectionViewDelegate {
    func collectionView(_ collectionView: UICollectionView, shouldDeselectItemAt indexPath: IndexPath) -> Bool {
        guard let item = dataSource?.itemIdentifier(for: indexPath) else { return false }
        switch item {
        case .category(let item):
            return item.name != viewModel.selectedCategory
        case .breed:
            return true
        case .image:
            return true
        }
    }

    func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        guard let item = dataSource?.itemIdentifier(for: indexPath) else { return }
        switch item {
        case .category(let item):
            collectionView.selectOneIndexInSection(at: indexPath, animated: true)
            viewModel.selectCategory(category: item.name)
            updateInBackground()
        case .breed(let breed):
            viewModel.toggleBreed(breed: breed.breed)
            updateInBackground()
        case .image(let image):
            print(image)
        }
    }

    func collectionView(_ collectionView: UICollectionView, didDeselectItemAt indexPath: IndexPath) {
        guard let item = dataSource?.itemIdentifier(for: indexPath) else { return }
        switch item {
        case .category(let category):
            print(category)
        case .breed(let breed):
            viewModel.toggleBreed(breed: breed.breed)
            updateInBackground()
        case .image(let image):
            print(image)
        }
    }
}
```

이 단계에서는 항목 선택 및 해제를 처리하기 위해 UICollectionViewDelegate 메서드를 구현했습니다. shouldDeselectItemAt 메서드를 사용하여 항목 유형에 따라 특정 항목의 선택 해제를 방지합니다. didSelectItemAt 메서드는 항목 선택을 처리하고 뷰 모델을 그에 맞게 업데이트합니다. didDeselectItemAt 메서드는 항목 해제를 처리하고 뷰 모델을 업데이트합니다.

컬렉션 뷰에서 여러 항목을 선택할 수 있도록 여러 선택이 가능하도록 설정했습니다. 이는 섹션 내에서 여러 항목을 선택할 수 있도록 하기 위해 각 항목 유형에 대한 선택 및 선택 해제 로직을 처리해야 한다는 것을 의미합니다.

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

경고: UICollectionView를 구성 레이아웃을 사용하도록 마이그레이션할 때 UICollectionViewDelegate가 업데이트되지 않은 모델을 참조하는 경우가 있습니다. UICollectionView의 진실의 원천은 diffable 데이터 소스이며 섹션 및 항목을 참조하는 데 사용해야 합니다. 이를 하지 않으면 앱 충돌이 발생할 수 있습니다.

우리는 비용을 들여 배웠습니다. UICollectionViewDelegate가 UICollectionViewDiffableDataSource를 포함하는 클래스에 구현되어야 하며 올바르지 않은 모델을 참조하는 것을 방지해야 합니다. 항상 dataSource?.itemIdentifier(for: indexPath) 및 dataSource?.sectionIdentifier(for: section)을 사용하여 항목과 섹션을 참조하고 UICollectionView에서 직접 검색하지 않도록 해야 합니다.

# 단계 9: 선택 관리를 위한 도우미 메서드 추가

섹션 내에서 선택 및 선택 해제를 처리하는 도우미 메서드를 추가하세요.

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

```swift
extension UICollectionView {
    func deselectAllInSection(section: Int, animated: Bool) {
        guard let selectedIndexesInSection = indexPathsForSelectedItems?
            .filter({  $0.section == section }) else { return }
        for index in selectedIndexesInSection {
            deselectItem(at: index, animated: animated)
        }
    }

    func selectOneIndexInSection(at indexPath: IndexPath, animated: Bool) {
        deselectAllInSectionExcept(at: indexPath, animated: animated)
        selectItem(at: indexPath, animated: animated, scrollPosition: [])
    }

    private func deselectAllInSectionExcept(at indexPath: IndexPath, animated: Bool) {
        guard let selectedIndexesInSection = indexPathsForSelectedItems?
            .filter({  $0.section == indexPath.section && $0.row != indexPath.row }) else { return }
        for index in selectedIndexesInSection {
            deselectItem(at: index, animated: animated)
        }
    }
}
```

# 요약

이제 UICollectionViewDiffableDataSource와 UICollectionViewCompositionalLayout을 사용하여 여러 필터링 가능한 섹션을 관리하는 MultiFilterViewController를 만들었습니다. 이 설정을 통해 동적이고 효율적인 데이터 업데이트와 유연한 항목 선택 및 선택 해제 처리가 가능해졌습니다. UICollectionView의 진리의 원천은 UICollectionViewDiffableDataSource이며, 항목 및 섹션을 참조할 때 사용해야 한다는 것을 배웠습니다. 이 자습서의 소스 코드는 여기에서 찾을 수 있습니다.

# 참고문헌

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

- Collection View 레이아웃의 진보
- UI 데이터 소스의 진보
- 현대적인 Collection Views 구현

Just Eat Takeaway.com에서 채용 중입니다! 함께 일하고 싶으신가요? 지금 지원하세요.
