<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "3c4738bb0836dd838c552ab9cab7e09d",
  "translation_date": "2025-09-04T00:39:58+00:00",
  "source_file": "6-NLP/4-Hotel-Reviews-1/README.md",
  "language_code": "ko"
}
-->
# 호텔 리뷰를 활용한 감정 분석 - 데이터 처리

이 섹션에서는 이전 강의에서 배운 기술을 활용하여 대규모 데이터셋에 대한 탐색적 데이터 분석을 수행합니다. 다양한 열의 유용성을 충분히 이해한 후, 다음을 배우게 됩니다:

- 불필요한 열을 제거하는 방법
- 기존 열을 기반으로 새로운 데이터를 계산하는 방법
- 최종 과제에서 사용할 결과 데이터셋을 저장하는 방법

## [강의 전 퀴즈](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/37/)

### 소개

지금까지 텍스트 데이터가 숫자 데이터와는 매우 다르다는 점을 배웠습니다. 사람이 작성하거나 말한 텍스트는 패턴, 빈도, 감정, 의미를 분석할 수 있습니다. 이번 강의에서는 실제 데이터셋과 실제 과제를 다룹니다: **[유럽의 515K 호텔 리뷰 데이터](https://www.kaggle.com/jiashenliu/515k-hotel-reviews-data-in-europe)**. 이 데이터는 [CC0: 퍼블릭 도메인 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)를 포함하며, Booking.com에서 공개 소스를 통해 수집되었습니다. 데이터셋의 제작자는 Jiashen Liu입니다.

### 준비 사항

다음이 필요합니다:

* Python 3로 실행 가능한 .ipynb 노트북
* pandas
* NLTK, [로컬에 설치 필요](https://www.nltk.org/install.html)
* Kaggle에서 제공되는 데이터셋 [유럽의 515K 호텔 리뷰 데이터](https://www.kaggle.com/jiashenliu/515k-hotel-reviews-data-in-europe). 압축 해제 시 약 230MB입니다. 이 NLP 강의와 관련된 루트 `/data` 폴더에 다운로드하세요.

## 탐색적 데이터 분석

이번 과제에서는 감정 분석과 게스트 리뷰 점수를 활용하여 호텔 추천 봇을 구축한다고 가정합니다. 사용하게 될 데이터셋에는 6개 도시의 1493개 호텔에 대한 리뷰가 포함되어 있습니다.

Python, 호텔 리뷰 데이터셋, NLTK의 감정 분석을 사용하여 다음을 알아낼 수 있습니다:

* 리뷰에서 가장 자주 사용되는 단어와 구문은 무엇인가요?
* 호텔을 설명하는 공식 *태그*가 리뷰 점수와 상관관계가 있나요? (예: 특정 호텔에 대해 *어린 자녀를 동반한 가족*의 리뷰가 *혼자 여행하는 사람*보다 더 부정적인가요? 이는 해당 호텔이 *혼자 여행하는 사람*에게 더 적합하다는 것을 나타낼 수 있습니다.)
* NLTK 감정 점수가 호텔 리뷰어의 숫자 점수와 '일치'하나요?

#### 데이터셋

다운로드한 데이터셋을 로컬에 저장한 후 탐색해 봅시다. VS Code나 Excel 같은 편집기에서 파일을 열어보세요.

데이터셋의 헤더는 다음과 같습니다:

*Hotel_Address, Additional_Number_of_Scoring, Review_Date, Average_Score, Hotel_Name, Reviewer_Nationality, Negative_Review, Review_Total_Negative_Word_Counts, Total_Number_of_Reviews, Positive_Review, Review_Total_Positive_Word_Counts, Total_Number_of_Reviews_Reviewer_Has_Given, Reviewer_Score, Tags, days_since_review, lat, lng*

다음과 같이 그룹화하면 더 쉽게 살펴볼 수 있습니다:
##### 호텔 관련 열

* `Hotel_Name`, `Hotel_Address`, `lat` (위도), `lng` (경도)
  * *lat*과 *lng*를 사용하여 Python으로 호텔 위치를 지도에 표시할 수 있습니다 (예: 부정적/긍정적 리뷰에 따라 색상 코딩)
  * Hotel_Address는 명확히 유용하지 않으므로, 더 쉽게 정렬 및 검색할 수 있도록 국가로 대체할 가능성이 있습니다.

**호텔 메타 리뷰 열**

* `Average_Score`
  * 데이터셋 제작자에 따르면, 이 열은 *지난 1년 동안의 최신 댓글을 기반으로 계산된 호텔의 평균 점수*를 나타냅니다. 이는 다소 특이한 점수 계산 방식으로 보이지만, 현재로서는 그대로 받아들일 수 있습니다.
  
  ✅ 이 데이터의 다른 열을 기반으로 평균 점수를 계산할 다른 방법이 떠오르나요?

* `Total_Number_of_Reviews`
  * 이 호텔이 받은 총 리뷰 수를 나타냅니다. (코드를 작성하지 않고는 이 값이 데이터셋의 리뷰를 의미하는지 명확하지 않습니다.)
* `Additional_Number_of_Scoring`
  * 리뷰어가 긍정적 또는 부정적 리뷰를 작성하지 않고 점수만 매긴 경우를 의미합니다.

**리뷰 관련 열**

- `Reviewer_Score`
  - 소수점 이하 한 자리까지 표현된 숫자 값으로, 최소 2.5에서 최대 10 사이입니다.
  - 왜 2.5가 최저 점수인지에 대한 설명은 없습니다.
- `Negative_Review`
  - 리뷰어가 아무것도 작성하지 않은 경우, 이 필드는 "**No Negative**"로 채워집니다.
  - 리뷰어가 부정적 리뷰 열에 긍정적 리뷰를 작성할 수도 있습니다 (예: "이 호텔에 나쁜 점은 없습니다").
- `Review_Total_Negative_Word_Counts`
  - 부정적 단어 수가 많을수록 점수가 낮아질 가능성이 높습니다 (감정 분석을 확인하지 않은 상태에서).
- `Positive_Review`
  - 리뷰어가 아무것도 작성하지 않은 경우, 이 필드는 "**No Positive**"로 채워집니다.
  - 리뷰어가 긍정적 리뷰 열에 부정적 리뷰를 작성할 수도 있습니다 (예: "이 호텔에는 좋은 점이 전혀 없습니다").
- `Review_Total_Positive_Word_Counts`
  - 긍정적 단어 수가 많을수록 점수가 높아질 가능성이 높습니다 (감정 분석을 확인하지 않은 상태에서).
- `Review_Date`와 `days_since_review`
  - 리뷰의 신선도 또는 오래됨을 측정할 수 있습니다 (예: 오래된 리뷰는 호텔 관리가 변경되었거나, 리노베이션이 이루어졌거나, 수영장이 추가되었기 때문에 정확하지 않을 수 있음).
- `Tags`
  - 리뷰어가 자신을 설명하기 위해 선택할 수 있는 짧은 설명입니다 (예: 혼자 여행, 가족, 숙박 유형, 숙박 기간, 리뷰 제출 방식 등).
  - 그러나 이러한 태그를 사용하는 데는 문제가 있습니다. 아래 섹션에서 유용성에 대해 논의합니다.

**리뷰어 관련 열**

- `Total_Number_of_Reviews_Reviewer_Has_Given`
  - 예를 들어, 리뷰를 많이 작성한 리뷰어(수백 개의 리뷰)가 긍정적 리뷰보다 부정적 리뷰를 더 자주 작성하는 경향이 있는지 확인할 수 있습니다. 그러나 특정 리뷰의 리뷰어는 고유 코드로 식별되지 않으므로 리뷰 세트와 연결할 수 없습니다. 100개 이상의 리뷰를 작성한 리뷰어는 30명 있지만, 이를 추천 모델에 어떻게 활용할 수 있을지는 불분명합니다.
- `Reviewer_Nationality`
  - 일부 사람들은 특정 국적이 긍정적 또는 부정적 리뷰를 작성할 가능성이 더 높다고 생각할 수 있습니다. 하지만 이러한 견해를 모델에 반영할 때 주의해야 합니다. 이는 국가적(때로는 인종적) 고정관념일 수 있으며, 각 리뷰어는 자신의 경험을 바탕으로 리뷰를 작성한 개인입니다. 이전 호텔 숙박 경험, 이동 거리, 개인 성격 등 여러 요인을 통해 필터링되었을 수 있습니다. 국적이 리뷰 점수의 이유라고 단정 짓기는 어렵습니다.

##### 예시

| Average  Score | Total Number   Reviews | Reviewer   Score | Negative <br />Review                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Positive   Review                 | Tags                                                                                      |
| -------------- | ---------------------- | ---------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------- | ----------------------------------------------------------------------------------------- |
| 7.8            | 1945                   | 2.5              | 이곳은 현재 호텔이 아니라 공사 현장입니다. 장거리 여행 후 휴식을 취하거나 방에서 작업하는 동안 아침 일찍부터 하루 종일 용납할 수 없는 공사 소음에 시달렸습니다. 사람들이 하루 종일 잭해머를 사용하며 인접한 방에서 작업했습니다. 방 교체를 요청했지만 조용한 방은 없었습니다. 설상가상으로 과도한 요금을 청구받았습니다. 이른 아침 비행기를 타야 했기 때문에 저녁에 체크아웃했으며 적절한 청구서를 받았습니다. 그러나 하루 후 호텔은 예약 가격을 초과하여 제 동의 없이 추가 요금을 청구했습니다. 끔찍한 곳입니다. 이곳을 예약하지 마세요. | 아무것도 없음. 끔찍한 곳. 멀리하세요. | 비즈니스 여행, 커플, 스탠다드 더블룸, 2박 숙박 |

보시다시피, 이 손님은 호텔에서 매우 불쾌한 경험을 했습니다. 이 호텔은 평균 점수 7.8과 1945개의 리뷰를 가지고 있지만, 이 리뷰어는 2.5점을 주고 115단어로 부정적인 경험을 작성했습니다. Positive_Review 열에 아무것도 작성하지 않았다면 긍정적인 점이 없다고 추측할 수 있었겠지만, 7단어로 경고를 남겼습니다. 단어 수만 계산하고 단어의 의미나 감정을 고려하지 않는다면 리뷰어의 의도를 왜곡할 수 있습니다. 이상하게도, 2.5점이라는 점수는 혼란스럽습니다. 호텔 숙박이 그렇게 나빴다면 왜 점수를 아예 주지 않았을까요? 데이터셋을 자세히 조사해보면 최저 점수가 2.5이고 0이 아님을 알 수 있습니다. 최고 점수는 10입니다.

##### 태그

위에서 언급했듯이, 처음에는 `Tags`를 사용하여 데이터를 분류하는 것이 합리적으로 보입니다. 그러나 이 태그는 표준화되어 있지 않아 문제가 됩니다. 예를 들어, 특정 호텔에서는 *싱글룸*, *트윈룸*, *더블룸*이라는 옵션이 있지만, 다른 호텔에서는 *디럭스 싱글룸*, *클래식 퀸룸*, *이그제큐티브 킹룸*이라는 옵션이 있습니다. 이들은 같은 것을 의미할 수 있지만, 변형이 너무 많아 다음과 같은 선택지가 생깁니다:

1. 모든 용어를 단일 표준으로 변경하려고 시도합니다. 그러나 각 경우에 변환 경로가 명확하지 않아 매우 어렵습니다 (예: *클래식 싱글룸*은 *싱글룸*으로 매핑되지만, *정원 또는 도시 전망이 있는 슈페리어 퀸룸*은 매핑하기 훨씬 어렵습니다).

1. NLP 접근 방식을 사용하여 *혼자*, *비즈니스 여행객*, *어린 자녀를 동반한 가족*과 같은 특정 용어의 빈도를 측정하고 이를 추천에 반영합니다.

태그는 일반적으로 (항상 그런 것은 아니지만) *여행 유형*, *게스트 유형*, *객실 유형*, *숙박 기간*, *리뷰 제출 방식*에 맞춰 5~6개의 쉼표로 구분된 값을 포함하는 단일 필드입니다. 그러나 일부 리뷰어가 각 필드를 채우지 않는 경우(빈칸으로 남겨두는 경우), 값이 항상 동일한 순서로 나타나지는 않습니다.

예를 들어, *그룹 유형*을 살펴보겠습니다. 이 필드에는 `Tags` 열에서 1025개의 고유한 가능성이 있으며, 불행히도 이 중 일부만 그룹을 나타냅니다(일부는 객실 유형 등). *가족*을 언급하는 항목만 필터링하면 *패밀리룸* 유형 결과가 많이 포함됩니다. *with*라는 용어를 포함하면, 즉 *Family with* 값을 계산하면 결과가 더 나아지며, "어린 자녀를 동반한 가족" 또는 "나이 든 자녀를 동반한 가족"이라는 문구가 515,000개의 결과 중 80,000개 이상 포함됩니다.

이는 태그 열이 완전히 쓸모없는 것은 아니지만, 유용하게 만들기 위해 약간의 작업이 필요함을 의미합니다.

##### 평균 호텔 점수

이 데이터셋에는 이해하기 어려운 이상점이나 불일치가 몇 가지 있지만, 모델을 구축할 때 이를 인지할 수 있도록 여기에 설명합니다. 이를 해결하면 토론 섹션에서 알려주세요!

데이터셋에는 평균 점수와 리뷰 수와 관련된 다음 열이 포함되어 있습니다:

1. Hotel_Name
2. Additional_Number_of_Scoring
3. Average_Score
4. Total_Number_of_Reviews
5. Reviewer_Score  

이 데이터셋에서 가장 많은 리뷰를 보유한 호텔은 *Britannia International Hotel Canary Wharf*로, 515,000개의 리뷰 중 4789개를 차지합니다. 그러나 이 호텔의 `Total_Number_of_Reviews` 값은 9086입니다. 리뷰가 없는 점수가 훨씬 더 많다고 추측할 수 있으므로, `Additional_Number_of_Scoring` 열 값을 추가해야 할 수도 있습니다. 해당 값은 2682이며, 4789에 더하면 7471이 됩니다. 하지만 여전히 `Total_Number_of_Reviews`에서 1615가 부족합니다.

`Average_Score` 열을 보면, 데이터셋의 리뷰 평균이라고 추측할 수 있지만, Kaggle 설명에 따르면 "*지난 1년 동안의 최신 댓글을 기반으로 계산된 호텔의 평균 점수*"라고 합니다. 이는 그다지 유용해 보이지 않지만, 데이터셋의 리뷰 점수를 기반으로 자체 평균을 계산할 수 있습니다. 같은 호텔을 예로 들면, 평균 호텔 점수는 7.1로 제공되지만, 데이터셋에서 계산된 점수(리뷰어 점수 평균)는 6.8입니다. 이는 비슷하지만 동일한 값은 아니며, `Additional_Number_of_Scoring` 리뷰에서 제공된 점수가 평균을 7.1로 증가시켰다고 추측할 수 있습니다. 그러나 이를 테스트하거나 입증할 방법이 없으므로, `Average_Score`, `Additional_Number_of_Scoring`, `Total_Number_of_Reviews`를 신뢰하거나 사용하는 것은 어렵습니다.

더 복잡한 점은, 두 번째로 리뷰 수가 많은 호텔의 계산된 평균 점수가 8.12이고 데이터셋의 `Average_Score`는 8.1입니다. 이 점수가 정확한 것은 우연일까요, 아니면 첫 번째 호텔의 데이터가 불일치한 것일까요?
이 호텔이 이상치일 가능성과 대부분의 값이 일치하지만 일부 값이 어떤 이유로 일치하지 않을 가능성을 고려하여, 데이터셋의 값을 탐색하고 올바른 사용 여부를 결정하기 위한 간단한 프로그램을 작성할 것입니다.

> 🚨 주의 사항
>
> 이 데이터셋을 작업할 때, 텍스트를 직접 읽거나 분석하지 않고 텍스트에서 무언가를 계산하는 코드를 작성하게 됩니다. 이것이 NLP의 본질이며, 사람이 직접 하지 않아도 의미나 감정을 해석하는 것입니다. 하지만 부정적인 리뷰를 읽게 될 가능성도 있습니다. 저는 여러분이 이를 읽지 않기를 권장합니다. 왜냐하면 읽을 필요가 없기 때문입니다. 일부 리뷰는 어리석거나 호텔과는 무관한 부정적인 리뷰일 수 있습니다. 예를 들어, "날씨가 좋지 않았다"는 호텔이나 누구도 통제할 수 없는 문제입니다. 하지만 리뷰에는 어두운 면도 있습니다. 때로는 부정적인 리뷰가 인종차별적, 성차별적, 또는 연령차별적일 수 있습니다. 이는 불행한 일이지만, 공개 웹사이트에서 스크랩된 데이터셋에서는 예상할 수 있는 일입니다. 일부 리뷰어는 불쾌하거나 불편하거나 마음이 상할 수 있는 리뷰를 남깁니다. 코드를 통해 감정을 측정하게 하고, 직접 읽고 마음이 상하지 않도록 하는 것이 좋습니다. 그렇다고 해도 이런 리뷰를 작성하는 사람은 소수에 불과하지만, 여전히 존재합니다.

## 연습 - 데이터 탐색
### 데이터 로드

데이터를 시각적으로 충분히 살펴봤으니, 이제 코드를 작성하여 답을 찾아봅시다! 이 섹션에서는 pandas 라이브러리를 사용합니다. 첫 번째 작업은 CSV 데이터를 로드하고 읽을 수 있는지 확인하는 것입니다. pandas 라이브러리는 빠른 CSV 로더를 제공하며, 결과는 이전 강의에서처럼 데이터프레임에 저장됩니다. 우리가 로드할 CSV는 50만 개 이상의 행과 17개의 열을 포함하고 있습니다. pandas는 데이터프레임과 상호작용할 수 있는 강력한 방법을 많이 제공합니다. 여기에는 모든 행에 대해 작업을 수행할 수 있는 기능도 포함됩니다.

이제 사용할 데이터 파일을 로드해 봅시다:

```python
# Load the hotel reviews from CSV
import pandas as pd
import time
# importing time so the start and end time can be used to calculate file loading time
print("Loading data file now, this could take a while depending on file size")
start = time.time()
# df is 'DataFrame' - make sure you downloaded the file to the data folder
df = pd.read_csv('../../data/Hotel_Reviews.csv')
end = time.time()
print("Loading took " + str(round(end - start, 2)) + " seconds")
```

데이터가 로드되었으니, 이제 이를 기반으로 작업을 수행할 수 있습니다. 다음 부분을 위해 프로그램 상단에 이 코드를 유지하세요.

## 데이터 탐색

이번 경우 데이터는 이미 *정리된 상태*입니다. 즉, 작업할 준비가 되어 있으며, 알고리즘이 영어 문자만 기대할 때 문제를 일으킬 수 있는 다른 언어의 문자가 포함되어 있지 않습니다.

✅ 데이터가 NLP 기술을 적용하기 전에 초기 처리가 필요한 경우가 있을 수 있지만, 이번에는 그렇지 않습니다. 만약 처리해야 한다면, 비영어 문자를 어떻게 처리할 것인지 생각해 보세요.

데이터가 로드된 후 코드를 사용하여 탐색할 수 있는지 확인하세요. `Negative_Review`와 `Positive_Review` 열에 집중하고 싶을 수 있습니다. 이 열들은 NLP 알고리즘이 처리할 자연어 텍스트로 채워져 있습니다. 하지만 잠시 기다리세요! NLP와 감정 분석에 뛰어들기 전에, 아래 코드를 따라 데이터셋에 제공된 값이 pandas로 계산한 값과 일치하는지 확인하세요.

## 데이터프레임 작업

이번 강의의 첫 번째 작업은 데이터프레임을 변경하지 않고 데이터를 검사하는 코드를 작성하여 다음 주장이 올바른지 확인하는 것입니다.

> 많은 프로그래밍 작업과 마찬가지로, 이를 완료하는 방법은 여러 가지가 있지만, 가장 간단하고 이해하기 쉬운 방법으로 작업하는 것이 좋습니다. 특히 나중에 이 코드를 다시 볼 때 이해하기 쉬운 방법으로 작업하는 것이 중요합니다. 데이터프레임에는 종종 원하는 작업을 효율적으로 수행할 수 있는 포괄적인 API가 있습니다.

다음 질문을 코딩 작업으로 간주하고, 솔루션을 보기 전에 답을 시도해 보세요.

1. 방금 로드한 데이터프레임의 *shape*을 출력하세요 (shape는 행과 열의 수를 나타냅니다).
2. 리뷰어 국적의 빈도수를 계산하세요:
   1. `Reviewer_Nationality` 열에 대해 고유한 값이 몇 개인지, 그리고 그 값들이 무엇인지 확인하세요.
   2. 데이터셋에서 가장 흔한 리뷰어 국적은 무엇인지 (국가와 리뷰 수를 출력) 확인하세요.
   3. 다음으로 가장 흔한 10개의 국적과 그 빈도수를 확인하세요.
3. 가장 흔한 10개의 리뷰어 국적에 대해 각 국적별로 가장 많이 리뷰된 호텔은 무엇인지 확인하세요.
4. 데이터셋에서 호텔별 리뷰 수를 계산하세요 (호텔의 빈도수).
5. 데이터셋의 각 호텔에 대해 모든 리뷰어 점수의 평균을 계산하여 `Calc_Average_Score`라는 새 열을 데이터프레임에 추가하세요. `Hotel_Name`, `Average_Score`, `Calc_Average_Score` 열을 출력하세요.
6. 호텔 중 `Average_Score`와 `Calc_Average_Score`가 동일한 (소수점 첫째 자리까지 반올림) 호텔이 있는지 확인하세요.
   1. Series(행)를 인수로 받아 값을 비교하고 값이 같지 않을 때 메시지를 출력하는 Python 함수를 작성해 보세요. 그런 다음 `.apply()` 메서드를 사용하여 모든 행을 함수로 처리하세요.
7. `Negative_Review` 열 값이 "No Negative"인 행이 몇 개인지 계산하고 출력하세요.
8. `Positive_Review` 열 값이 "No Positive"인 행이 몇 개인지 계산하고 출력하세요.
9. `Positive_Review` 열 값이 "No Positive"이고 `Negative_Review` 열 값이 "No Negative"인 행이 몇 개인지 계산하고 출력하세요.

### 코드 답안

1. 방금 로드한 데이터프레임의 *shape*을 출력하세요 (shape는 행과 열의 수를 나타냅니다).

   ```python
   print("The shape of the data (rows, cols) is " + str(df.shape))
   > The shape of the data (rows, cols) is (515738, 17)
   ```

2. 리뷰어 국적의 빈도수를 계산하세요:

   1. `Reviewer_Nationality` 열에 대해 고유한 값이 몇 개인지, 그리고 그 값들이 무엇인지 확인하세요.
   2. 데이터셋에서 가장 흔한 리뷰어 국적은 무엇인지 (국가와 리뷰 수를 출력) 확인하세요.

   ```python
   # value_counts() creates a Series object that has index and values in this case, the country and the frequency they occur in reviewer nationality
   nationality_freq = df["Reviewer_Nationality"].value_counts()
   print("There are " + str(nationality_freq.size) + " different nationalities")
   # print first and last rows of the Series. Change to nationality_freq.to_string() to print all of the data
   print(nationality_freq) 
   
   There are 227 different nationalities
    United Kingdom               245246
    United States of America      35437
    Australia                     21686
    Ireland                       14827
    United Arab Emirates          10235
                                  ...  
    Comoros                           1
    Palau                             1
    Northern Mariana Islands          1
    Cape Verde                        1
    Guinea                            1
   Name: Reviewer_Nationality, Length: 227, dtype: int64
   ```

   3. 다음으로 가장 흔한 10개의 국적과 그 빈도수를 확인하세요.

      ```python
      print("The highest frequency reviewer nationality is " + str(nationality_freq.index[0]).strip() + " with " + str(nationality_freq[0]) + " reviews.")
      # Notice there is a leading space on the values, strip() removes that for printing
      # What is the top 10 most common nationalities and their frequencies?
      print("The next 10 highest frequency reviewer nationalities are:")
      print(nationality_freq[1:11].to_string())
      
      The highest frequency reviewer nationality is United Kingdom with 245246 reviews.
      The next 10 highest frequency reviewer nationalities are:
       United States of America     35437
       Australia                    21686
       Ireland                      14827
       United Arab Emirates         10235
       Saudi Arabia                  8951
       Netherlands                   8772
       Switzerland                   8678
       Germany                       7941
       Canada                        7894
       France                        7296
      ```

3. 가장 흔한 10개의 리뷰어 국적에 대해 각 국적별로 가장 많이 리뷰된 호텔은 무엇인지 확인하세요.

   ```python
   # What was the most frequently reviewed hotel for the top 10 nationalities
   # Normally with pandas you will avoid an explicit loop, but wanted to show creating a new dataframe using criteria (don't do this with large amounts of data because it could be very slow)
   for nat in nationality_freq[:10].index:
      # First, extract all the rows that match the criteria into a new dataframe
      nat_df = df[df["Reviewer_Nationality"] == nat]   
      # Now get the hotel freq
      freq = nat_df["Hotel_Name"].value_counts()
      print("The most reviewed hotel for " + str(nat).strip() + " was " + str(freq.index[0]) + " with " + str(freq[0]) + " reviews.") 
      
   The most reviewed hotel for United Kingdom was Britannia International Hotel Canary Wharf with 3833 reviews.
   The most reviewed hotel for United States of America was Hotel Esther a with 423 reviews.
   The most reviewed hotel for Australia was Park Plaza Westminster Bridge London with 167 reviews.
   The most reviewed hotel for Ireland was Copthorne Tara Hotel London Kensington with 239 reviews.
   The most reviewed hotel for United Arab Emirates was Millennium Hotel London Knightsbridge with 129 reviews.
   The most reviewed hotel for Saudi Arabia was The Cumberland A Guoman Hotel with 142 reviews.
   The most reviewed hotel for Netherlands was Jaz Amsterdam with 97 reviews.
   The most reviewed hotel for Switzerland was Hotel Da Vinci with 97 reviews.
   The most reviewed hotel for Germany was Hotel Da Vinci with 86 reviews.
   The most reviewed hotel for Canada was St James Court A Taj Hotel London with 61 reviews.
   ```

4. 데이터셋에서 호텔별 리뷰 수를 계산하세요 (호텔의 빈도수).

   ```python
   # First create a new dataframe based on the old one, removing the uneeded columns
   hotel_freq_df = df.drop(["Hotel_Address", "Additional_Number_of_Scoring", "Review_Date", "Average_Score", "Reviewer_Nationality", "Negative_Review", "Review_Total_Negative_Word_Counts", "Positive_Review", "Review_Total_Positive_Word_Counts", "Total_Number_of_Reviews_Reviewer_Has_Given", "Reviewer_Score", "Tags", "days_since_review", "lat", "lng"], axis = 1)
   
   # Group the rows by Hotel_Name, count them and put the result in a new column Total_Reviews_Found
   hotel_freq_df['Total_Reviews_Found'] = hotel_freq_df.groupby('Hotel_Name').transform('count')
   
   # Get rid of all the duplicated rows
   hotel_freq_df = hotel_freq_df.drop_duplicates(subset = ["Hotel_Name"])
   display(hotel_freq_df) 
   ```
   |                 Hotel_Name                 | Total_Number_of_Reviews | Total_Reviews_Found |
   | :----------------------------------------: | :---------------------: | :-----------------: |
   | Britannia International Hotel Canary Wharf |          9086           |        4789         |
   |    Park Plaza Westminster Bridge London    |          12158          |        4169         |
   |   Copthorne Tara Hotel London Kensington   |          7105           |        3578         |
   |                    ...                     |           ...           |         ...         |
   |       Mercure Paris Porte d Orleans        |           110           |         10          |
   |                Hotel Wagner                |           135           |         10          |
   |            Hotel Gallitzinberg             |           173           |          8          |
   
   데이터셋에서 계산된 결과가 `Total_Number_of_Reviews` 값과 일치하지 않는 것을 알 수 있습니다. 이 값이 호텔의 총 리뷰 수를 나타내지만, 모든 리뷰가 스크랩되지 않았거나 다른 계산이 이루어진 것일 수 있습니다. `Total_Number_of_Reviews`는 이러한 불확실성 때문에 모델에서 사용되지 않습니다.

5. 데이터셋의 각 호텔에 대해 모든 리뷰어 점수의 평균을 계산하여 `Calc_Average_Score`라는 새 열을 데이터프레임에 추가하세요. `Hotel_Name`, `Average_Score`, `Calc_Average_Score` 열을 출력하세요.

   ```python
   # define a function that takes a row and performs some calculation with it
   def get_difference_review_avg(row):
     return row["Average_Score"] - row["Calc_Average_Score"]
   
   # 'mean' is mathematical word for 'average'
   df['Calc_Average_Score'] = round(df.groupby('Hotel_Name').Reviewer_Score.transform('mean'), 1)
   
   # Add a new column with the difference between the two average scores
   df["Average_Score_Difference"] = df.apply(get_difference_review_avg, axis = 1)
   
   # Create a df without all the duplicates of Hotel_Name (so only 1 row per hotel)
   review_scores_df = df.drop_duplicates(subset = ["Hotel_Name"])
   
   # Sort the dataframe to find the lowest and highest average score difference
   review_scores_df = review_scores_df.sort_values(by=["Average_Score_Difference"])
   
   display(review_scores_df[["Average_Score_Difference", "Average_Score", "Calc_Average_Score", "Hotel_Name"]])
   ```

   데이터셋 평균과 계산된 평균이 왜 때때로 다른지 궁금할 수 있습니다. 일부 값이 일치하지만 다른 값에 차이가 있는 이유를 알 수 없으므로, 이 경우 우리가 가진 리뷰 점수를 사용하여 평균을 직접 계산하는 것이 가장 안전합니다. 그렇다고 해도 차이는 일반적으로 매우 작습니다. 데이터셋 평균과 계산된 평균 간의 가장 큰 차이를 가진 호텔은 다음과 같습니다:

   | Average_Score_Difference | Average_Score | Calc_Average_Score |                                  Hotel_Name |
   | :----------------------: | :-----------: | :----------------: | ------------------------------------------: |
   |           -0.8           |      7.7      |        8.5         |                  Best Western Hotel Astoria |
   |           -0.7           |      8.8      |        9.5         | Hotel Stendhal Place Vend me Paris MGallery |
   |           -0.7           |      7.5      |        8.2         |               Mercure Paris Porte d Orleans |
   |           -0.7           |      7.9      |        8.6         |             Renaissance Paris Vendome Hotel |
   |           -0.5           |      7.0      |        7.5         |                         Hotel Royal Elys es |
   |           ...            |      ...      |        ...         |                                         ... |
   |           0.7            |      7.5      |        6.8         |     Mercure Paris Op ra Faubourg Montmartre |
   |           0.8            |      7.1      |        6.3         |      Holiday Inn Paris Montparnasse Pasteur |
   |           0.9            |      6.8      |        5.9         |                               Villa Eugenie |
   |           0.9            |      8.6      |        7.7         |   MARQUIS Faubourg St Honor Relais Ch teaux |
   |           1.3            |      7.2      |        5.9         |                          Kube Hotel Ice Bar |

   점수 차이가 1을 초과하는 호텔이 단 1개뿐이므로, 차이를 무시하고 계산된 평균 점수를 사용할 수 있을 것입니다.

6. `Negative_Review` 열 값이 "No Negative"인 행이 몇 개인지 계산하고 출력하세요.

7. `Positive_Review` 열 값이 "No Positive"인 행이 몇 개인지 계산하고 출력하세요.

8. `Positive_Review` 열 값이 "No Positive"이고 `Negative_Review` 열 값이 "No Negative"인 행이 몇 개인지 계산하고 출력하세요.

   ```python
   # with lambdas:
   start = time.time()
   no_negative_reviews = df.apply(lambda x: True if x['Negative_Review'] == "No Negative" else False , axis=1)
   print("Number of No Negative reviews: " + str(len(no_negative_reviews[no_negative_reviews == True].index)))
   
   no_positive_reviews = df.apply(lambda x: True if x['Positive_Review'] == "No Positive" else False , axis=1)
   print("Number of No Positive reviews: " + str(len(no_positive_reviews[no_positive_reviews == True].index)))
   
   both_no_reviews = df.apply(lambda x: True if x['Negative_Review'] == "No Negative" and x['Positive_Review'] == "No Positive" else False , axis=1)
   print("Number of both No Negative and No Positive reviews: " + str(len(both_no_reviews[both_no_reviews == True].index)))
   end = time.time()
   print("Lambdas took " + str(round(end - start, 2)) + " seconds")
   
   Number of No Negative reviews: 127890
   Number of No Positive reviews: 35946
   Number of both No Negative and No Positive reviews: 127
   Lambdas took 9.64 seconds
   ```

## 또 다른 방법

람다 없이 항목을 세고, 행을 세기 위해 sum을 사용하는 또 다른 방법:

   ```python
   # without lambdas (using a mixture of notations to show you can use both)
   start = time.time()
   no_negative_reviews = sum(df.Negative_Review == "No Negative")
   print("Number of No Negative reviews: " + str(no_negative_reviews))
   
   no_positive_reviews = sum(df["Positive_Review"] == "No Positive")
   print("Number of No Positive reviews: " + str(no_positive_reviews))
   
   both_no_reviews = sum((df.Negative_Review == "No Negative") & (df.Positive_Review == "No Positive"))
   print("Number of both No Negative and No Positive reviews: " + str(both_no_reviews))
   
   end = time.time()
   print("Sum took " + str(round(end - start, 2)) + " seconds")
   
   Number of No Negative reviews: 127890
   Number of No Positive reviews: 35946
   Number of both No Negative and No Positive reviews: 127
   Sum took 0.19 seconds
   ```

   `Negative_Review`와 `Positive_Review` 열 값이 각각 "No Negative"와 "No Positive"인 행이 127개 있다는 것을 알 수 있습니다. 이는 리뷰어가 호텔에 숫자 점수를 부여했지만 긍정적 또는 부정적 리뷰를 작성하지 않았다는 것을 의미합니다. 다행히도 이러한 행은 소수(515738개 중 127개, 약 0.02%)에 불과하므로, 모델이나 결과에 특정 방향으로 영향을 미치지 않을 것입니다. 하지만 리뷰가 없는 리뷰 데이터셋에 이러한 행이 있다는 것을 예상하지 못했을 수 있으므로, 데이터를 탐색하여 이러한 행을 발견하는 것이 중요합니다.

이제 데이터셋을 탐색했으니, 다음 강의에서는 데이터를 필터링하고 감정 분석을 추가할 것입니다.

---
## 🚀도전 과제

이번 강의는 이전 강의에서 본 것처럼, 데이터를 이해하고 작업을 수행하기 전에 데이터의 특성을 파악하는 것이 얼마나 중요한지 보여줍니다. 특히 텍스트 기반 데이터는 신중히 검토해야 합니다. 다양한 텍스트 중심 데이터셋을 탐색하고 모델에 편향이나 왜곡된 감정을 도입할 수 있는 영역을 발견할 수 있는지 확인해 보세요.

## [강의 후 퀴즈](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/38/)

## 복습 및 자기 학습

[NLP 학습 경로](https://docs.microsoft.com/learn/paths/explore-natural-language-processing/?WT.mc_id=academic-77952-leestott)를 통해 음성 및 텍스트 중심 모델을 구축할 때 사용할 도구를 알아보세요.

## 과제 

[NLTK](assignment.md)

---

**면책 조항**:  
이 문서는 AI 번역 서비스 [Co-op Translator](https://github.com/Azure/co-op-translator)를 사용하여 번역되었습니다. 정확성을 위해 최선을 다하고 있으나, 자동 번역에는 오류나 부정확성이 포함될 수 있습니다. 원본 문서를 해당 언어로 작성된 상태에서 권위 있는 자료로 간주해야 합니다. 중요한 정보의 경우, 전문적인 인간 번역을 권장합니다. 이 번역 사용으로 인해 발생할 수 있는 오해나 잘못된 해석에 대해 당사는 책임을 지지 않습니다.  