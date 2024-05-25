---
title: "PDF 파싱 해부 02 파이프라인 기반 방법"
description: ""
coverImage: "/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_0.png"
date: 2024-05-23 17:36
ogImage: 
  url: /assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_0.png
tag: Tech
originalTitle: "Demystifying PDF Parsing 02: Pipeline-Based Method"
link: "https://medium.com/ai-advances/demystifying-pdf-parsing-02-pipeline-based-method-82619dbcbddf"
---


PDF 파일 및 스캔된 이미지와 같은 비구조화된 문서를 구조화 또는 반구조화된 형식으로 변환하는 것은 인공 지능의 핵심 요소입니다. 그러나 PDF의 복잡성 및 PDF 파싱 작업의 복잡성으로 인해이 프로세스는 신비로움을 지니게 됩니다.

본 시리즈의 목적은 PDF 파싱을 분명하게 하는 데 있습니다. 이전 글에서는 PDF 파싱의 주요 작업을 소개하고 기존 방법을 분류하며 각 방법에 대한 간단한 소개를 제공했습니다.

이 글에서는 파이프라인 기반 방법에 초점을 맞춥니다. 먼저 개요를 살펴보고, 그런 다음 몇 가지 대표적인 파이프라인 기반 PDF 파싱 프레임워크의 구현 전략을 소개하여 얻은 통찰을 공유하겠습니다.

# 개요

<div class="content-ad"></div>

Pipeline 기반 방법은 PDF를 구문 분석하는 작업을 모델 또는 알고리즘의 파이프라인으로 보는 방식으로, 그림 1에 나타난 것과 같습니다.

Pipeline 기반 방법은 다음 다섯 단계로 나뉠 수 있습니다:

- 원본 PDF 파일을 전 처리하여 흐림 또는 왜곡된 방향과 같은 문제를 해결합니다. 이미지 개선, 이미지 방향 보정 등 해당 단계에는 이와 같은 작업이 포함됩니다.
- 레이아웃 분석을 수행합니다. 이 과정에는 시각 구조 분석과 의미 구조 분석이 포함됩니다. 전자는 문서의 구조를 식별하고 유사한 영역의 윤곽을 나타내며, 후자는 이러한 영역을 텍스트, 제목, 목록, 표, 도표 등과 같은 특정 문서 유형으로 레이블링합니다. 이 단계에는 페이지의 읽기 순서를 분석하는 것도 포함됩니다.
- 레이아웃 분석 중 식별된 각 영역을 분리하여 처리합니다. 이 과정에는 테이블 이해, 텍스트 인식, 수식, 흐름도, 특수 기호 인식 등 다른 구성 요소 식별이 포함됩니다.
- 이전 결과를 통합하여 페이지 구조를 복원합니다.
- Markdown, JSON 또는 HTML과 같은 구조화되거나 반구조화된 정보를 출력합니다.

이어서, 이 글에서는 대표적인 Pipeline 기반 PDF 구문 분석 프레임워크 몇 가지를 살펴보고 그로부터 얻은 통찰을 공유할 것입니다.

<div class="content-ad"></div>

# 마커

마커는 딥 러닝 모델을 위한 파이프라인입니다. PDF, EPUB 및 MOBI 문서를 Markdown 형식으로 변환할 수 있는 기능을 갖추고 있습니다.

## 전체 프로세스

그림 2에 설명된 대로, 마커의 전체 프로세스는 다음 네 단계로 나뉘어집니다:

<div class="content-ad"></div>


Step 1: PyMuPDF와 OCR을 사용하여 페이지를 블록으로 나누고 텍스트를 추출합니다. 해당 코드는 다음과 같습니다:

```js
def convert_single_pdf(
        fname: str,
        model_lst: List,
        max_pages=None,
        metadata: Optional[Dict]=None,
        parallel_factor: int = 1
) -> Tuple[str, Dict]:
    ...
    ...
    doc = pymupdf.open(fname, filetype=filetype)
    if filetype != "pdf":
        conv = doc.convert_to_pdf()
        doc = pymupdf.open("pdf", conv)

    blocks, toc, ocr_stats = get_text_blocks(
        doc,
        tess_lang,
        spell_lang,
        max_pages=max_pages,
        parallel=int(parallel_factor * settings.OCR_PARALLEL_WORKERS)
    )
```

Step 2: 레이아웃 세그먼터를 활용하여 블록을 분류하고, 열 탐지기를 사용하여 블록의 순서를 정합니다. 해당 코드는 다음과 같습니다:

```js
def convert_single_pdf(
        fname: str,
        model_lst: List,
        max_pages=None,
        metadata: Optional[Dict]=None,
        parallel_factor: int = 1
) -> Tuple[str, Dict]:
    ...
    ...
    # 리스트에서 모델 분리
    texify_model, layoutlm_model, order_model, edit_model = model_lst

    block_types = detect_document_block_types(
        doc,
        blocks,
        layoutlm_model,
        batch_size=int(settings.LAYOUT_BATCH_SIZE * parallel_factor)
    )

    # 헤더와 푸터 찾기
    bad_span_ids = filter_header_footer(blocks)
    out_meta["block_stats"] = {"header_footer": len(bad_span_ids)}

    annotate_spans(blocks, block_types)

    # 플래그가 설정되어 있으면 디버그 데이터 덤프
    dump_bbox_debug_data(doc, blocks)

    blocks = order_blocks(
        doc,
        blocks,
        order_model,
        batch_size=int(settings.ORDERER_BATCH_SIZE * parallel_factor)
    )
    ...
    ...
```

<div class="content-ad"></div>

3단계: 헤더 및 푸터 필터링, 코드 및 표 블록 수정, 수식을 위한 Texify 모델 적용하는 절차입니다. 해당 코드는 다음과 같습니다:

```js
def convert_single_pdf(
        fname: str,
        model_lst: List,
        max_pages=None,
        metadata: Optional[Dict]=None,
        parallel_factor: int = 1
) -> Tuple[str, Dict]:
    ...
    ...
    # 코드 블록 수정
    code_block_count = identify_code_blocks(blocks)
    out_meta["block_stats"]["code"] = code_block_count
    indent_blocks(blocks)

    # 표 블록 수정
    merge_table_blocks(blocks)
    table_count = create_new_tables(blocks)
    out_meta["block_stats"]["table"] = table_count

    for page in blocks:
        for block in page.blocks:
            block.filter_spans(bad_span_ids)
            block.filter_bad_span_types()

    filtered, eq_stats = replace_equations(
        doc,
        blocks,
        block_types,
        texify_model,
        batch_size=int(settings.TEXIFY_BATCH_SIZE * parallel_factor)
    )
    out_meta["block_stats"]["equations"] = eq_stats
    ...
    ...
```

4단계: 에디터 모델을 사용하여 텍스트 후처리합니다. 해당 코드는 다음과 같습니다:

```js
def convert_single_pdf(
        fname: str,
        model_lst: List,
        max_pages=None,
        metadata: Optional[Dict]=None,
        parallel_factor: int = 1
) -> Tuple[str, Dict]:
    ...
    ...
    # 원본 데이터 변경을 피하기 위해 복사
    merged_lines = merge_spans(filtered)
    text_blocks = merge_lines(merged_lines, filtered)
    text_blocks = filter_common_titles(text_blocks)
    full_text = get_full_text(text_blocks)

    # 조인된 빈 블록 처리
    full_text = re.sub(r'\n{3,}', '\n\n', full_text)
    full_text = re.sub(r'(\n\s){3,}', '\n\n', full_text)

    # 점 표시 문자를 -로 교체
    full_text = replace_bullets(full_text)

    # 에디터 모델로 텍스트 후처리
    full_text, edit_stats = edit_full_text(
        full_text,
        edit_model,
        batch_size=settings.EDITOR_BATCH_SIZE * parallel_factor
    )
    out_meta["postprocess_stats"] = {"edit": edit_stats}

    return full_text, out_meta
```

<div class="content-ad"></div>

## Marker로부터 얻은 통찰

지금까지 Marker의 전반적인 프로세스를 소개했습니다. 이제 Marker로부터 얻은 통찰에 대해 지금 이야기해 보겠습니다.

통찰 1: 레이아웃 분석은 여러 하위 작업으로 나뉠 수 있습니다. 첫 번째 하위 작업은 PyMuPDF API를 호출하여 페이지 블록을 가져오는 것입니다.

```js
def ocr_entire_page(page, lang: str, spellchecker: Optional[SpellChecker] = None) -> List[Block]:
    if settings.OCR_ENGINE == "tesseract":
        return ocr_entire_page_tess(page, lang, spellchecker)
    elif settings.OCR_ENGINE == "ocrmypdf":
        return ocr_entire_page_ocrmp(page, lang, spellchecker)
    else:
        raise ValueError(f"알 수 없는 OCR 엔진입니다: {settings.OCR_ENGINE}")


def ocr_entire_page_tess(page, lang: str, spellchecker: Optional[SpellChecker] = None) -> List[Block]:
    try:
        full_tp = page.get_textpage_ocr(flags=settings.TEXT_FLAGS, dpi=settings.OCR_DPI, full=True, language=lang)
        blocks = page.get_text("dict", sort=True, flags=settings.TEXT_FLAGS, textpage=full_tp)["blocks"]
        full_text = page.get_text("text", sort=True, flags=settings.TEXT_FLAGS, textpage=full_tp)

        if len(full_text) == 0:
            return []

        # OCR 작업 여부 확인. 작업되지 않았다면 빈 목록 반환
        # 스캔된 빈 페이지에 연상되는 약한 텍스트 인쇄가 있는 경우 OCR가 실패할 수 있음
        if detect_bad_ocr(full_text, spellchecker):
            return []
    except RuntimeError:
        return []
    return blocks
```

<div class="content-ad"></div>

인사이트 2: 레이아웃LMv3와 같은 작은 다중 모달 사전 훈련 모델을 특정 작업에 맞게 세밀하게 조정하는 것이 매우 유익합니다. 예를 들어, Marker는 레이아웃LMv3을 세밀하게 조정하여 블록 유형을 감지하는 레이아웃 세그먼터 모델을 얻었습니다.

```python
def load_layout_model():
    model = LayoutLMv3ForTokenClassification.from_pretrained(
        settings.LAYOUT_MODEL_NAME,
        torch_dtype=settings.MODEL_DTYPE,
    ).to(settings.TORCH_DEVICE_MODEL)

    model.config.id2label = {
        0: "Caption",
        1: "Footnote",
        2: "Formula",
        3: "List-item",
        4: "Page-footer",
        5: "Page-header",
        6: "Picture",
        7: "Section-header",
        8: "Table",
        9: "Text",
        10: "Title"
    }

    model.config.label2id = {v: k for k, v in model.config.id2label.items()}
    return model
```

이 세밀 조정에 사용된 데이터셋은 공개 데이터셋 DocLayNet에서 수집했습니다.

인사이트 3: PDF 파일이 단일 열인지 또는 이중 열인지 확인하여 읽기 순서를 설정하는 것은 PDF 구문 분석에서 중요합니다. Marker의 방법은 레이아웃LMv3을 세밀하게 조정하여 열 감지기 모델을 만드는 것입니다. 이 모델은 페이지의 열 수를 결정하고, 그런 다음 중간 점 방법을 적용합니다.

<div class="content-ad"></div>

```js
def add_column_counts(doc, doc_blocks, model, batch_size):
    for i in range(0, len(doc_blocks), batch_size):
        batch = range(i, min(i + batch_size, len(doc_blocks)))
        rgb_images = []
        bboxes = []
        words = []
        for pnum in batch:
            page = doc[pnum]
            rgb_image, page_bboxes, page_words = get_inference_data(page, doc_blocks[pnum])
            rgb_images.append(rgb_image)
            bboxes.append(page_bboxes)
            words.append(page_words)

        predictions = batch_inference(rgb_images, bboxes, words, model)
        for pnum, prediction in zip(batch, predictions):
            doc_blocks[pnum].column_count = prediction


def order_blocks(doc, doc_blocks: List[Page], model, batch_size=settings.ORDERER_BATCH_SIZE):
    add_column_counts(doc, doc_blocks, model, batch_size)

    for page_blocks in doc_blocks:
        if page_blocks.column_count > 1:
            # Resort blocks based on position
            split_pos = page_blocks.x_start + page_blocks.width / 2
            left_blocks = []
            right_blocks = []
            for block in page_blocks.blocks:
                if block.x_start <= split_pos:
                    left_blocks.append(block)
                else:
                    right_blocks.append(block)
            page_blocks.blocks = left_blocks + right_blocks
    return doc_blocks
```

해당 내용은 Advanced RAG 02: Unveiling PDF Parsing에서 사용한 방식과 유사합니다.

인사이트 4: 전문 모델은 수학 공식을 처리하는 데 사용할 수 있습니다. 예를 들어 Marker의 Texify 모델은 도넛 아키텍처를 사용합니다. 해당 모델은 웹에서 수집한 라텍스 이미지와 해당 방정식을 사용하여 도넛 모델로 훈련되었습니다. 이 모델은 im2latex 데이터셋을 활용했습니다. 훈련은 대략 2일간 4대의 A6000에서 진행되었으며, 약 6회 에폭에 해당합니다.

인사이트 5: 모델은 후처리에도 사용할 수 있습니다. 주요 아이디어는 거의 마지막에 다가간 텍스트를 받아 예비 텍스트를 개선하여 불필요한 부분을 제거하고 공백을 추가하며 새로운 줄을 삽입하는 T5 모델을 훈련시키는 것입니다.
```

<div class="content-ad"></div>

```python
def load_editing_model():
    if not settings.ENABLE_EDITOR_MODEL:
        return None

    model = T5ForTokenClassification.from_pretrained(
            settings.EDITOR_MODEL_NAME,
            torch_dtype=settings.MODEL_DTYPE,
        ).to(settings.TORCH_DEVICE_MODEL)
    model.eval()

    model.config.label2id = {
        "equal": 0,
        "delete": 1,
        "newline-1": 2,
        "space-1": 3,
    }
    model.config.id2label = {v: k for k, v in model.config.label2id.items()}
    return model
```

현재 후처리기의 훈련과 데이터셋 구축에 대한 추가 세부 정보는 발견되지 않았습니다.

## Marker의 단점

당연히, Marker의 몇 가지 단점도 있습니다:
```

<div class="content-ad"></div>

- 전문 레이아웃 분석 모델은 훈련되거나 세밀하게 조정되지 않았으며, 대신 PyMuPDF의 기본 기능을 사용했습니다. 이 접근 방식의 효과는 의문스럽습니다.
- 테이블 인식 효과가 그리 좋지 않으며, 테이블 제목을 인식하지 못하는 문제가 있습니다. 이는 Nougat(OCR를 사용하지 않은 소형 모델 기반 솔루션으로 다음 글에서 자세히 소개될 것입니다)만큼 효과적이지 않습니다. 예를 들어, Figure 3는 "Attention Is All You Need"의 Table 3의 테이블 인식 결과를 보여줍니다. 왼쪽에는 원본 테이블이, 가운데에는 Marker를 사용한 결과가, 오른쪽에는 Nougat의 결과가 표시됩니다.

3. 영어와 유사한 언어만 지원합니다. 일본어나 힌디어 같은 언어는 작동하지 않습니다.

# PaperMage

Papermage는 시각적으로 풍부하고 구조화된 과학 문서의 분석 및 처리를 위한 오픈 소스 프레임워크입니다. 문서 내의 텍스트 및 시각적 요소를 명확하고 직관적으로 표현하고 조작하기 위한 완벽한 추상화를 제공합니다.

<div class="content-ad"></div>

Papermage는 각기 다른 자연어 처리 (NLP) 및 컴퓨터 비전 (CV) 모델을 단일 프레임워크로 통합합니다. Papermage는 일반적인 과학 문서 처리 시나리오에 대한 사용 준비 완료 솔루션을 제공합니다.

다음으로, PaperMage의 원리를 설명하고 소스 코드와 함께 전체 프로세스를 논의할 것입니다. 그런 다음, PaperMage에서 얻은 인사이트에 대해 논의할 것입니다.

## 구성 요소

Papermage는 주로 세 가지 부분으로 구성됩니다:

<div class="content-ad"></div>

- Magelib: 시각적으로 풍부한 문서를 다중 모달 구조로 나타내고 조작하기 위한 기본 요소와 방법을 포함하는 라이브러리입니다.
- Predictors: 다양한 첨단 과학 문서 분석 모델들을 통합하여 통일된 인터페이스로 구현한 것입니다. 이는 개별 모델이 서로 다른 프레임워크에 작성되었거나 다른 모드에서 작동하는 경우에도 가능합니다.
- Recipes: 종종 단일 모드인 개별 모듈들의 테스트된 조합에 쉽게 접근할 수 있게 하며, 이를 통해 복잡하고 확장 가능한 다중 모달 파이프라인을 형성할 수 있습니다.

## 기본 데이터 클래스

Magelib은 시각적으로 풍부하고 구조화된 문서의 기본 요소를 나타내기 위한 세 가지 기본 데이터 클래스를 제공합니다: 문서(Document), 레이어(Layers) 및 엔티티(Entities).

문서 및 레이어

<div class="content-ad"></div>

Figure 4는 PaperMage가 문서를 생성하고 표현하는 방법을 보여줍니다.

![Figure 4](/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_0.png)

문서 구조가 다양한 알고리즘이나 모델에 의해 추출되면, PaperMage는 텍스트와 시각적 정보를 모두 저장하는 주석 레이어로 개념화합니다.

나중에 우리는 레시피의 `run()` 함수의 구체적인 실행 과정을 소스 코드와 함께 분석할 것입니다.

<div class="content-ad"></div>

개체

그림 5에서 보듯이, 엔티티는 다중 모달 콘텐츠 단위를 나타냅니다.

![이미지](/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_1.png)

열/페이지에 걸친 문장이나 부유 그래픽/각주로 인해 중단된 문장과 같은 불연속 형태의 단위를 어떻게 관리합니까?

<div class="content-ad"></div>

PaperMage는 두 개의 멤버 변수인 spans와 boxes를 사용합니다. 그림 5에서 볼 수 있듯이 spans는 모든 기호 중 문장의 텍스트를 식별하고, boxes는 페이지에서의 시각적 좌표를 매핑합니다. 이 접근 방식은 섬세한 레이아웃 차이를 수용하는 유연성을 제공합니다.

게다가, 우리는 다른 방식으로 엔티티에 쉽게 접근할 수 있습니다. 이는 그림 6에서 보는 것처럼입니다.

아래 이미지는 운세 시스템의 구조를 설명하고 있는 URL을 통해 확인할 수 있습니다.

보다 깊이 PaperMage를 이해하기 위해, PDF 파싱의 구체적인 예시에서 시작하여 그로부터 자세히 설명하겠습니다.

<div class="content-ad"></div>

## 전체 프로세스 및 코드 분석

시험용 코드는 다음과 같습니다.

```js
from papermage.recipes import CoreRecipe

core_recipe = CoreRecipe()

doc = core_recipe.run("YOUR_PDF_PATH")
```

먼저 core_recipe = CoreRecipe()은 CoreRecipe 클래스의 생성자에 들어가게 되는데, 관련 라이브러리 및 모델의 초기화가 이루어집니다.

<div class="content-ad"></div>

```python
class CoreRecipe(Recipe):
    def __init__(
        self,
        ivila_predictor_path: str = "allenai/ivila-row-layoutlm-finetuned-s2vl-v2",
        bio_roberta_predictor_path: str = "allenai/vila-roberta-large-s2vl-internal",
        svm_word_predictor_path: str = "https://ai2-s2-research-public.s3.us-west-2.amazonaws.com/mmda/models/svm_word_predictor.tar.gz",
        dpi: int = 72,
    ):
        self.logger = logging.getLogger(self.__class__.__name__)
        self.dpi = dpi

        self.logger.info("Recipe를 생성하는 중...")
        self.parser = PDFPlumberParser()
        self.rasterizer = PDF2ImageRasterizer()

        # with warnings.catch_warnings():
        #     warnings.simplefilter("ignore")
        #     self.word_predictor = SVMWordPredictor.from_path(svm_word_predictor_path)

        self.publaynet_block_predictor = LPEffDetPubLayNetBlockPredictor.from_pretrained()
        self.ivila_predictor = IVILATokenClassificationPredictor.from_pretrained(ivila_predictor_path)
        self.sent_predictor = PysbdSentencePredictor()
        self.logger.info("Recipe 생성 완료")
```

Recipe 클래스는 CoreRecipe 클래스의 부모 클래스이므로, core_recipe.run() 함수는 Recipe::run()으로 이동할 것입니다.

```python
class Recipe:
    @abstractmethod
    def run(self, input: Any) -> Document:
        if isinstance(input, Path):
            if input.suffix == ".pdf":
                return self.from_pdf(pdf=input)
            if input.suffix == ".json":
                return self.from_json(doc=input)

            raise NotImplementedError("지원되지 않는 파일 유형입니다.")

        if isinstance(input, Document):
            return self.from_doc(doc=input)

        if isinstance(input, str):
            if os.path.exists(input):
                input = Path(input)
                return self.run(input=input)
            else:
                return self.from_str(text=input)

        raise NotImplementedError("지원되지 않는 문서 입력 형식입니다.")
```

그런 다음 CoreRecipe 클래스의 from_pdf()와 from_doc() 함수로 이어질 것입니다.

<div class="content-ad"></div>

```python
class CoreRecipe(Recipe):
    ...
    ...
    def from_pdf(self, pdf: Path) -> Document:
        self.logger.info("문서 파싱 중...")
        doc = self.parser.parse(input_pdf_path=pdf)

        self.logger.info("문서 래스터화 중...")
        images = self.rasterizer.rasterize(input_pdf_path=pdf, dpi=self.dpi)
        doc.annotate_images(images=list(images))
        self.rasterizer.attach_images(images=images, doc=doc)
        return self.from_doc(doc=doc)

    def from_doc(self, doc: Document) -> Document:
        # self.logger.info("단어 예측 중...")
        # words = self.word_predictor.predict(doc=doc)
        # doc.annotate_layer(name=WordsFieldName, entities=words)

        self.logger.info("문장 예측 중...")
        sentences = self.sent_predictor.predict(doc=doc)
        doc.annotate_layer(name=SentencesFieldName, entities=sentences)

        self.logger.info("블록 예측 중...")
        with warnings.catch_warnings():
            warnings.simplefilter("ignore")
            blocks = self.publaynet_block_predictor.predict(doc=doc)
        doc.annotate_layer(name=BlocksFieldName, entities=blocks)

        self.logger.info("도형 및 표 예측 중...")
        figures = []
        tables = []
        for block in blocks:
            if block.metadata.type == "Figure":
                figure = Entity(boxes=block.boxes)
                figures.append(figure)
            elif block.metadata.type == "Table":
                table = Entity(boxes=block.boxes)
                tables.append(table)
        doc.annotate_layer(name=FiguresFieldName, entities=figures)
        doc.annotate_layer(name=TablesFieldName, entities=tables)

        # self.logger.info("vila 예측 중...")
        vila_entities = self.ivila_predictor.predict(doc=doc)
        doc.annotate_layer(name="vila_entities", entities=vila_entities)

        for entity in vila_entities:
            entity.boxes = [
                Box.create_enclosing_box(
                    [b for t in doc.intersect_by_span(entity, name=TokensFieldName) for b in t.boxes]
                )
            ]
            # entity.text = make_text(entity=entity, document=doc)
        preds = group_by(entities=vila_entities, metadata_field="label", metadata_values_map=VILA_LABELS_MAP)
        doc.annotate(*preds)
        return doc
```

Figure 7에서 전체적인 프로세스를 보여줍니다:

Figure 7는 PaperMage의 처리 흐름이 파이프라인 방식을 따른다는 것을 보여줍니다.

처음에는 PDFPlumber 라이브러리를 사용하여 레이아웃 분석을 수행합니다. 그 후 레이아웃 분석 결과를 바탕으로 페이지의 다른 엔티티를 파싱하는 데 전문 알고리즘 또는 모델을 사용합니다. 이에는 문장, 도형, 표, 제목 등이 포함됩니다.
```

<div class="content-ad"></div>

다음으로, 우리는 세 가지 알고리즘 또는 모델을 논의할 것입니다:

- 문장 분리
- 레이아웃 구조 분석
- 논리적 구조 분석.

## 문장 분리

문장 분리에 사용되는 알고리즘은 PySBD로, rule-based 문장 경계 해석 Python 패키지입니다.

<div class="content-ad"></div>

표를 마크다운 형식으로 변경해주세요.

```js
[
Unannotated Entity: {'spans': [[0, 212]]}, 
Unannotated Entity: {'spans': [[212, 367]]},  
…
]
```

## 레이아웃 구조 분석

페이지의 레이아웃 구조를 분석하는 데 사용된 모델은 LPEffDetPubLayNetBlockPredictor입니다. 이는 LayoutParser에서 제공하는 깊은 학습 기반의 효율적인 객체 감지 모델입니다. 주요 기능은 문서를 시각적 블록 영역으로 분할하는 것입니다.

<div class="content-ad"></div>

이 페이지의 이미지는 doc.images로 참조됩니다. 결과는 각 블록에 대한 상자 클래스 개체와 해당 유형입니다. 상자에는 왼쪽 상단 꼭지점의 x 좌표, 왼쪽 상단 꼭지점의 y 좌표, 페이지 너비, 페이지 높이 및 페이지 번호가 포함됩니다.

```js
[
Unannotated Entity: {'boxes': [[0.5179840190298606, 0.752760137345049, 0.3682081491355128, 0.15176369855069774, 0]], 'metadata': {'type': 'Text'}, 
Unannotated Entity: {'boxes': [[0.5145780320135539, 0.5080924136055337, 0.3675624668198144, 0.23725746136663078, 0]], 'metadata': {'type': 'Text'}, 
…
]
```

## 논리 구조 분석

문서의 논리적 구조를 분석하는 데 사용된 모델은 IVILATokenClassificationPredictor입니다. 이는 제목, 초록, 본문, 각주, 캡션 등과 같은 조직 단위로 문서를 분리합니다.

<div class="content-ad"></div>

제공된 입력은 딕셔너리 형식의 페이지 수준 데이터입니다.

```js
{
    'words': ['word1', 'word2', ...],
    'bbox': [[x1, y1, x2, y2], [x1, y1, x2, y2], ...],
    'block_ids': [0, 0, 0, 1 ...],
    'line_ids': [0, 1, 1, 2 ...],
    'labels': [0, 0, 0, 1 ...], # 비어 있을 수도 있습니다
}
```

결과는 각 엔티티의 범위입니다.

```js
[
Unannotated Entity: {'spans': [[0, 80]], 'metadata': {'label': 'Title'}, 
Unannotated Entity: {'spans': [[81, 157]], 'metadata': {'label': 'Author'}, 
Unannotated Entity: {'spans': [[158, 215]], 'metadata': {'label': 'Paragraph'}, 
...
]
```

<div class="content-ad"></div>

## PaperMage에 대한 통찰과 토론

PDF 구문 분석의 추상화

PDF 구문 분석 작업에 있어서 PaperMage에서 제안한 추상화는 효과적입니다. 전체 PDF를 문서, 레이어 및 엔티티와 같은 유형으로 나누는 것은 분류와 관리를 용이하게 합니다.

확장성

<div class="content-ad"></div>

PaperMage는 쉽게 확장할 수 있는 프레임워크를 설계했습니다. 이는 개발자들이 후속 개발을 수행하기에 편리하게 만들어 줍니다.

예를 들어, 사용자 정의 예측기를 추가하려면 BasePredictor 기본 클래스로부터 상속받고 _predict() 함수를 재정의해주기만 하면 됩니다.

```js
from .base_predictor import BasePredictor

class YOUR_NEW_Predictor(BasePredictor):
    ...
    ...
    def _predict(self, doc: Document) -> List[YOUR_RET_TYPE]:
    ...
    ...
```

병렬화에 대해

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 바꿔보세요.

<div class="content-ad"></div>

다음으로, 비구조화된 프레임워크로부터 얻은 통찰과 경험에 대해 주로 논의하겠습니다. 특히, 이 프레임워크가 자체 PDF 구문 분석 도구를 개발하는 데 어떻게 도움이 되는지에 대해 설명하겠습니다.

## 레이아웃 분석에 대해

비구조화된 프레임워크의 레이아웃 분석은 자세하게 수행됩니다.

`strategy=hi_res`를 설정하면 YOLOX 또는 detectron2와 같은 모델을 레이아웃 분석에 활용합니다. 이는 PDFMiner와 결합되어 추가적인 감지가 이루어집니다. 두 결과물을 병합하여 최종 레이아웃을 생성하게 됩니다. 이것은 그림 8에 나타나 있습니다.

<div class="content-ad"></div>

Figure 9과 Figure 10은 BERT 논문의 16페이지 레이아웃 분석 결과의 시각화를 보여줍니다. 사진 속 상자들은 각 영역의 범위를 나타냅니다. Figure 9에 나타난 물체 탐지 모델의 결과는 더 정확하며, 더 많은 통합된 표와 이미지를 보여줍니다. 반면에 Figure 10에 표시된 PDFMiner의 탐지 결과는 표와 이미지 내용을 분리합니다.

<img src="/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_3.png" />

<img src="/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_4.png" />

레이아웃을 병합하는 구체적인 코드는 다음과 같습니다. PDFMiner 탐지 결과(extracted_layout)와 물체 탐지 모델의 결과(inferred_layout) 간의 각 영역 사이의 관계를 평가하는 이중 루프로 이루어져 있습니다. 그 후 병합 여부를 결정합니다.

<div class="content-ad"></div>

```python
def merge_inferred_layout_with_extracted_layout(
    inferred_layout: Collection[LayoutElement],
    extracted_layout: Collection[TextRegion],
    page_image_size: tuple,
    same_region_threshold: float = inference_config.LAYOUT_SAME_REGION_THRESHOLD,
    subregion_threshold: float = inference_config.LAYOUT_SUBREGION_THRESHOLD,
) -> List[LayoutElement]:
    """Merge two layouts to produce a single layout."""
    extracted_elements_to_add: List[TextRegion] = []
    inferred_regions_to_remove = []
    w, h = page_image_size
    full_page_region = Rectangle(0, 0, w, h)
    for extracted_region in extracted_layout:
        extracted_is_image = isinstance(extracted_region, ImageTextRegion)
        if extracted_is_image:
            # Skip extracted images for this purpose, we don't have the text from them and they
            # don't provide good text bounding boxes.

            is_full_page_image = region_bounding_boxes_are_almost_the_same(
                extracted_region.bbox,
                full_page_region,
                FULL_PAGE_REGION_THRESHOLD,
            )

            if is_full_page_image:
                continue
        region_matched = False
        for inferred_region in inferred_layout:
            if inferred_region.source in CHIPPER_VERSIONS:
                continue
            ...
            ...
```

## 사용자 정의에 관해

비정형 프레임워크에는 쉬운 사용자 정의를 가능케 하는 다양한 중간 결과가 있습니다.

이전 글에서는 비정형 데이터의 세 가지 도전 과제를 다루었습니다.

<div class="content-ad"></div>

- 테이블 파싱
- 감지된 블록 재배열, 특히 이중 열 PDF의 경우
- 다중 수준 제목 추출

마지막 두 가지 도전 과제는 중간 구조를 수정하여 해결할 수 있습니다. 예를 들어, Figure 11은 BERT 논문의 두 번째 페이지의 최종 레이아웃을 보여줍니다.

![이미지](/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_5.png)

동시에 레이아웃 분석 결과를 쉽게 얻을 수 있습니다.

<div class="content-ad"></div>

```js
[

LayoutElement(bbox=Rectangle(x1=851.1539916992188, y1=181.15073777777613, x2=1467.844970703125, y2=587.8204599999975), text='CR이 MST보다 세분화된 영역에 대해 일반화되어 왔다.(Kiros et al., 2015; Logeswaran and Lee, 2018) 나 문단 임베딩(Le and Mikolov, 2014)과 같은 세분 화된 영역', source=<Source.YOLOX: 'yolox'>, type='Text', prob=0.9519357085227966, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=196.5296173095703, y1=181.1507377777777, x2=815.468994140625, y2=512.548237777777), text='word based only on its context. Unlike left-to-right language model pre-training, the MLM ob- jective enables the representation to fuse the left and the right context, which allows us to pre- In addi- train a deep bidirectional Transformer. tion to the masked language model, we also use a “next sentence prediction” task that jointly pre- trains text-pair representations. The contributions of our paper are as follows: ', source=<Source.YOLOX: 'yolox'>, type='Text', prob=0.9517233967781067, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=200.22352600097656, y1=539.1451822222216, x2=825.0242919921875, y2=870.542682222221), text='• 우리는 양방향 사전 학습이 언어 표현에 중요함을 입증합니다. 라드포드 외(2018)는 사전 훈련을 위해 단방향 언어 모델을 사용하였지만, BERT는 가려진 언어 모델을 사용하여 사전 훈련된 깊은 양방향 표현을 가능하게 합니다. 이는 또한 Peter et al. (2018a)와 대조적으로, 왼쪽에서 오른쪽으로 훈련된 독립적인 왼쪽에서 오른쪽 및 오른쪽에서 왼쪽 LM의 얕은 결합을 사용합니다.', source=<Source.YOLOX: 'yolox'>, type='List-item', prob=0.9414362907409668, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=851.8727416992188, y1=599.8257377777753, x2=1468.0499267578125, y2=1420.4982377777742), text='ELMo 및 그 전신(Peter et al., 2017, 2018a)은 전통적인 단어 임베딩 연구를 다른 차원으로 일반화합니다. 그들은 왼쪽에서 오른쪽으로와 오른쪽에서 왼쪽으로 언어 모델에서 문맥-주의적인 기능을 추출합니다. 각 토큰의 맥락적 표현은 왼쪽에서 오른쪽 및 오른쪽에서 왼쪽 표현의 연결입니다. 문맥적 단어 임베딩을 기존의 작업별 아키텍처와 통합할 때, ELMo는 질문 응답(Rajpurkar et al., 2016), 감성 분석(Socher et al., 2013) 및 명명된 개체 인식(Tjong Kim Sang 및 De Meulder, 2003)을 포함한 여러 주요 NLP 벤치마크에서 기술의 최신 동향을 선보입니다. Melamud et., 2016은 LSTM을 사용하여 왼쪽 및 오른쪽 컨텍스트에서 단일 단어를 예측하는 작업을 통해 컨텍스트 표현을 학습하는 것을 제안했습니다. ELMo와 유사하게, 그들의 모델은 기능 기반이며 깊게 양방향적이지 않습니다. Fedus et al.(2018)은 클로즈 태스크가 텍스트 생성 모델의 견고성을 향상시키는 데 사용될 수 있다는 것을 보여줍니다.', source=<Source.YOLOX: 'yolox'>, type='Text', prob=0.938507616519928, image_path=None, parent=None),


LayoutElement(bbox=Rectangle(x1=199.3734130859375, y1=900.5257377777765, x2=824.69873046875, y2=1156.648237777776), text='• 사전 훈련된 표현이 많은 과도하게 엔지니어링된 과제-별 아키텍처의 필요성을 줄인다는 것을 보여줍니다. BERT는 다양한 문장 수준 및 토큰 수준 작업에 대해 최첨단 성능을 달성하는 첫 번째 세세 조정 기반 표현 모델이며, 많은 과제별 아키텍처를 능가합니다.', source=<Source.YOLOX: 'yolox'>, type='List-item', prob=0.9461237788200378, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=195.5695343017578, y1=1185.526123046875, x2=815.9393920898438, y2=1330.3272705078125), text='• BERT는 열한가지 NLP 작업에 대한 최신 기술을 선도합니다. 코드와 사전 훈련 모델은 다음에서 제공됩니다. https://github.com/ google-research/bert.', source=<Source.YOLOX: 'yolox'>, type='List-item', prob=0.9213815927505493, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=195.33956909179688, y1=1360.7886962890625, x2=447.47264000000007, y2=1397.038330078125), text='2 Related Work ', source=<Source.YOLOX: 'yolox'>, type='Section-header', prob=0.8663332462310791, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=197.7477264404297, y1=1419.3353271484375, x2=817.3308715820312, y2=1527.54443359375), text='There is a long history of pre-training general lan- guage representations, and we brieﬂy review the most widely-used approaches in this section. ', source=<Source.YOLOX: 'yolox'>, type='Text', prob=0.928022563457489, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=851.0028686523438, y1=1468.341394166663, x2=1420.4693603515625, y2=1498.6444497222187), text='2.2 Unsupervised Fine-tuning Approaches ', source=<Source.YOLOX: 'yolox'>, type='Section-header', prob=0.8346447348594666, image_path=None, parent=None),

LayoutElement(bbox=Rectangle(x1=853.5444444444446, y1=1526.3701822222185, x2=1470.989990234375, y2=1669.5843488888852), text='컨텍스트 기반 기법과 마찬가지로, 이 방향으로 첫 번째 작업은 라벨이 지정되지 않은 텍스트로부터 단어 em-(Col- bed 파라미터를 사전 훈련하는 것에 있습니다.(lobert and Weston, 2008).', source=<Source.YOLOX: 'yolox'>, type='Text',

<div class="content-ad"></div>

테이블 탐지 및 인식에는 비구조화 된 프레임 워크에서 Table Transformer가 사용됩니다.

Table Transformer 모델은 미구조 문서에서의 포괄적인 테이블 추출을 위해 PubTables-1M에서 제안되었습니다. 본 논문은 새로운 데이터 세트 인 PubTables-1M을 소개하며, 미구조 문서에서의 테이블 추출 및 테이블 구조 인식 및 기능 분석 작업을 수행하기 위해 설계되었습니다. Figure 12에서 확인할 수 있습니다.

Table Transformer은 PubTables-1M 데이터셋에서 DETR 모델을 기반으로 훈련되었으며, 테이블 탐지 및 테이블 구조 인식과 같은 작업을 위해 사용됩니다.

<div class="content-ad"></div>

아래 표 처리 방법은 이전 글을 참고해 주세요.

## 수식 검출 및 인식에 대해

구조화되지 않은 프레임워크에는 수식 검출 및 인식에 전담된 모듈이 부족하여 Figure 13에 표시된 것처럼 보통 성능을 보입니다.

![수식 검출 및 인식](/assets/img/2024-05-23-DemystifyingPDFParsing02Pipeline-BasedMethod_7.png)

<div class="content-ad"></div>

# 결론

본 글은 PDF 파싱에 대한 파이프라인 기반 방법에 대한 개요를 제공했습니다. 세 가지 대표적인 프레임워크를 예시로 사용하여 이 접근 방식을 탐구하며 깊이 있는 소개와 이를 통해 얻은 통찰을 공유했습니다.

요약하자면,

- Marker는 몇 가지 단점이 있지만 가벼우면서 빠른 도구입니다.
- PaperMage는 주로 과학 문서용으로 설계되었지만 미래 개발을 지원하는 탁월한 확장성을 갖고 있습니다.
- Unstructured는 포괄적인 파이프라인 기반 PDF 파싱 프레임워크입니다. 그 이점은 자세한 레이아웃 분석과 강력한 사용자 정의 기능에 있습니다.

<div class="content-ad"></div>

일반적으로, 파이프라인 기반 PDF 구문 분석 방법은 해석 가능하며 사용하기 쉬워 많이 사용되는 PDF 구문 분석 방법입니다. 그러나 효과적인지는 각 모델 또는 알고리즘의 성능에 크게 의존합니다. 따라서 훈련 데이터와 각 모델의 구조는 신중하게 설계되어야 합니다.

PDF 구문 분석이나 문서 인텔리전스에 관심이 있다면 다른 글도 읽어보세요.

또한, 최신 AI 관련 콘텐츠는 뉴스레터에서 확인할 수 있습니다.

마지막으로, 이 글에 오류나 누락 사항이 있다면 댓글 섹션에서 지적해주시거나 공유할 생각이 있다면 언제든지 알려주세요.