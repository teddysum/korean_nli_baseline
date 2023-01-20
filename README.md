# korean_NLI_baseline

본 소스 코드는 '확신성_추론' 과제를 위한 베이스라인 모델 및 학습과 평가를 위한 코드입니다.

코드는 'nli_main.py'이며 ' train.sh'를 이용하여 학습할 수 있습니다. 또 'demo.sh' 를 학습된 모델에 활용하여 결과를 생성한 뒤, 'test.sh'를 실행하면 결과물에 대한 평가를 할 수 있습니다.



## 데이터
확신성 추론 데이터 세트의 output은 1에서 7사이의 실수로 표현되어 있습니다. context를 고려하였을 때 작성자가 prompt가 참이라고 확신하는 경우 7점(함의), prompt가 거짓이라고 확신하는 경우 1점을 부여합니다(모순). 즉, 작성자가 prompt에 대해 확신하는 정도를 1점부터 7점까지로 표현한 데이터 세트입니다.


과제 데이터 세트는 국립국어원 인공 지능의 언어 능력 평가( https://corpus.korean.go.kr )에서 내려받을 수 있습니다.  

#### 예시
``` 
{"id": "nikluge-2022-nli-train-000001", "input": {"context": "준플레이오프가 5전3선승제로 바뀐 2008년 이후 하위팀이 상위팀을 두 번이나 꺾고 한국시리즈에 오른 것은 두산이 처음이다. 두산은 마지막 우승이었던 지난 2001년에도 준플레이오프부터 시리즈를 시작해 한국시리즈 우승을 차지한 바 있다. 두산은 마지막 우승 당시 한국시리즈 상대가 삼성이었음을 기억하고 있을 것이다.", "prompt": "두산의 마지막 우승 당시의 한국시리즈 상대는 삼성이었다."}, "output": "6.375"}
{"id": "nikluge-2022-nli-train-000002", "input": {"context": "그렇다. 김대중 전 대통령이 북한과 협상할 때 강조하는 '모개흥정'과 닮은꼴이다. 그러나 그가 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈음을 기억하면 별로 새삼스러운 일도 아니다.", "prompt": "그는 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈다."}, "output": "6.75"}
{"id": "nikluge-2022-nli-train-000003", "input": {"context": "니콜라 사르코지 프랑스 대통령은 지난해 중국으로부터 228억달러의 투자를 유치하기 위해 “프랑스는 다른 문화를 존중한다”고 말했다. 이는 중국이 인권문제로 선진국들로부터 비판받을 때마다 내세웠던 논리다. 사르코지가 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있음을 기억하면 중국의 경제적 힘에 체면을 버린 셈이다.", "prompt": "사르코지는 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있다."}, "output": "6.375"}
{"id": "nikluge-2022-nli-train-000004", "input": {"context": "지난 13~19일 부산문화회관 소극장에서 공연된 부산시립극단(이하 시립극단) 제 41회 공연<지금... 여기(noWhere; 의도적으로 단어를 붙임으로써 <아무 데도 없다>라고도 읽히도록)>를 본 관객이라면, 막이 내리는 순간 극장안은 온통 눈물바다였음을 기억할 것이다.", "prompt": "<지금... 여기>의 막이 내리는 순간 극장 안은 온통 눈물바다였다."}, "output": "6.75"}
{"id": "nikluge-2022-nli-train-000005", "input": {"context": "언어영역의 경우 모든 문제의 답은 지문 안에 있음을 기억해야 한다.", "prompt": "언어영역의 경우 모든 문제의 답은 지문 안에 있다."}, "output": "6.625"}
{"id": "nikluge-2022-nli-train-000006", "input": {"context": "트랙타가 일하는 소를 사라지게 하고, 길바닥에 쇠똥이 나뒹굴지 않으니 쇠똥구리가 경단을 만들 수 없고, 쇠똥구리의 퇴각이 지구환경에 또 어떤 영향을 미칠지는 알 수 없는 노릇이다. 경제현상도 마찬가진데 IMF환란 때도 따져보면 그랬고, 1930년대 대공황이 미국 어느 시골은행의 부도로부터 시작되었음을 기억한다면 이것은 극명한 나비효과의 한 사례인 것이다.", "prompt": "1930년대 대공황은 미국 어느 시골 은행의 부도로부터 시작되었다."}, "output": "6.875"}
{"id": "nikluge-2022-nli-train-000007", "input": {"context": "문 대표가 어제 회견에서 국정 최대 현안인 공무원연금 개혁에 대해 일언반구도 언급하지 않은 점 역시 실망스럽다. 문 대표는 박 대통령과 여야 회동에서 '공무원연금 개혁 시한인 5월 2일을 결코 가볍게 여기지 않겠다'고 말한 것을 기억할 것이다.", "prompt": "문 대표는 '공무원연금 개혁 시한인 5월 2일을 결코 가볍게 여기지 않겠다'고 말했다."}, "output": "6.25"}
{"id": "nikluge-2022-nli-train-000008", "input": {"context": "작가는 수년전부터 울란바토르 대학에서 한국관광을 강의한다고 한다. 그가 몽골에서 느끼는 한국은 한국을 경유한 노동자들의 인상 등을 통해서다. 예외도 있지만 아마도 많은 이들이 한국에 가서 고생하고 모멸 받았던 것을 기억하는 것 같다.", "prompt": "한국을 경유한 많은 몽골 노동자들은 한국에 가서 고생하고 모멸 받았다."}, "output": "5.625"}
{"id": "nikluge-2022-nli-train-000009", "input": {"context": "그렇지만 자가혈당측정법을 실천하는 당뇨병 환자일수록 혈당 조절이 잘돼 합병증 발병률이 낮다는 연구 결과가 있다. 매일 혈당 측정을 해야 한다는 것을 기억하면 생활할 때 자연스럽게 혈당을 높이는 습관을 피하게 된다는 것이다.", "prompt": "자가혈당측정법을 실천하는 당뇨병 환자는 매일 혈당 측정을 해야 한다."}, "output": "5.875"}
{"id": "nikluge-2022-nli-train-000010", "input": {"context": "남은 시간 동안 교과 내용을 다시 한 번 살펴보고 자신이 약한 단원을 복습하자. 그리고 논리적인 글쓰기가 필요한 생물 논제의 경우 다른 과목보다 채점 요소가 많다는 것을 기억해야 한다.", "prompt": "논리적인 글쓰기가 필요한 생물 논제는 다른 과목보다 채점 요소가 많다."}, "output": "6.625"}
{"id": "nikluge-2022-nli-train-000011", "input": {"context": "투표 당일인 13일 유권자는 투표소에서 두 차례에 걸쳐 투표용지를 나눠받는다. 기표소에 두 번 들어가야 한다고 기억하면 쉽다.", "prompt": "유권자는 기표소에 두 번 들어가야 한다."}, "output": "6.75"}
{"id": "nikluge-2022-nli-train-000012", "input": {"context": "또 하나 잘 틀리면서 어려워하는 어휘가 '두루뭉술하다'는 표현이다. 보통 두리뭉술하다, 두루뭉실하다, 두리뭉실하다 등으로 적는데 모든 잘못된 표기다. '우'자 돌림이라고 기억하면 비교적 오래도록 잊지 않을 듯하다.", "prompt": "'두루뭉술하다'는 '우'자 돌림이다."}, "output": "6.75"}
{"id": "nikluge-2022-nli-train-000013", "input": {"context": "페루는 우리나라 사람들에게 남미의 태평양 국가로 해안선을 따라 길게 뻗어 있는 칠레의 옆 나라,잉카 제국의 후예,세계 7대 불가사의 중 하나인 '마추픽추'가 있는 나라라는 정도의 이미지를 갖고 있다. 조금 더 관심을 가진 사람이라면 일본계 알베르토 후지모리 대통령이 1990년부터 2000년 말까지 10년 이상을 통치한 나라라고 기억할 것이다.", "prompt": "페루는 일본계 알베르토 후지모리 대통령이 1990년부터 2000년 말까지 10년 이상을 통치한 나라이다."}, "output": "7"}
{"id": "nikluge-2022-nli-train-000014", "input": {"context": "우리는 우리 나름대로 알맞게 쓰던 말을 잘 살려서 써 주면 됩니다. 수수하게 쓰던 말이 가장 알맞으면서 더없이 빛나는 말임을 깨달으면 아무런 걱정이 없습니다.", "prompt": "수수하게 쓰던 말이 가장 알맞으면서 더없이 빛나는 말이다."}, "output": "5.75"}
{"id": "nikluge-2022-nli-train-000015", "input": {"context": "영어로 말한 내용을 녹음해서 들어보면 자신이 사용하는 어휘를 점검할 수 있다. 5분정도 영어로 말한 내용을 녹음해 그대로 받아 적어보자. 말할 때는 의식하지 못했던 문법적 오류를 찾아낼 수 있을 뿐 아니라 아는 어휘에 비해 실제 사용하는 어휘가 적음을 깨달을 것이다.", "prompt": "아는 어휘에 비해 실제 사용하는 어휘가 적다."}, "output": "6.5"}
```

#### 데이터 전처리
모델을 학습하기 위한 데이터 전처리는 소스코드의 tokenize_and_align_labels(tokenizer, form, value, max_len) 함수와 get_dataset(raw_data, tokenizer, max_len) 함수를 참고하시면 됩니다. tokenize_and_align_labels에서 데이터 형태를 1차 가공하고 get_dataset에서 pytorch의 DataLoader를 이용하기 위한 TensorDataset 형태로 가공합니다.


## 모델 구성

xlm-roberta-base( https://huggingface.co/xlm-roberta-base )를 기반으로 학습하였습니다.

모델 구조는 xlm-roberta-base 모델의 \<s> 토큰 output에 FFRegression을 붙인 형태의 모델입니다.

학습된 baseline 모델은 아래 링크에서 받으실 수 있습니다.

모델 링크: https://drive.google.com/file/d/1JFr4JpIbaT0sC2DETQVUiqUD5Kf_CS-g/view?usp=share_link

모델 입력 형태는 '\<s>context\</s>\</s>prompt\</s>'이며 확신성 추론 결과에 해당하는 실수를 output으로 나타냅니다. 

####입력 예시
```
<s>준플레이오프가 5전3선승제로 바뀐 2008년 이후 하위팀이 상위팀을 두 번이나 꺾고 한국시리즈에 오른 것은 두산이 처음이다. 두산은 마지막 우승이었던 지난 2001년에도 준플레이오프부터 시리즈를 시작해 한국시리즈 우승을 차지한 바 있다. 두산은 마지막 우승 당시 한국시리즈 상대가 삼성이었음을 기억하고 있을 것이다.</s></s>두산의 마지막 우승 당시의 한국시리즈 상대는 삼성이었다.</s>
<s>그렇다. 김대중 전 대통령이 북한과 협상할 때 강조하는 '모개흥정'과 닮은꼴이다. 그러나 그가 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈음을 기억하면 별로 새삼스러운 일도 아니다.</s></s>그는 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈다.</s>
<s>니콜라 사르코지 프랑스 대통령은 지난해 중국으로부터 228억달러의 투자를 유치하기 위해 “프랑스는 다른 문화를 존중한다”고 말했다. 이는 중국이 인권문제로 선진국들로부터 비판받을 때마다 내세웠던 논리다. 사르코지가 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있음을 기억하면 중국의 경제적 힘에 체면을 버린 셈이다.</s></s>사르코지는 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있다.</s>
...
```

####출력 예시 - 실수
```
"6.375"
"6.75"
"6.375"
...
```

#### 평가
baseline 코드에서 제공된 평가 코드로 평가하였을때, 아래와 같이 결과가 나왔습니다.

train 과정에서 --do_eval을 argument로 전달하면 매 epoch마다 dev data에 대해 평가 결과를 보여줍니다.

demo.sh을 이용하여 결과물을 추출한뒤 평가 데이터를 이용하여 test.sh와 같이 평가할 수 있습니다.

모델을 이용하여 pred_data와 같은 형태의 데이터를 만들기 위한 방법은 demo.sh 파일을 참고하면 됩니다.

평가함수는 evaluation(y_true, y_pred) 함수를 이용하면 되고, 입력 데이터는 아래와 같습니다.


true_data
``` 
{"id": "nikluge-2022-nli-train-000001", "input": {"context": "준플레이오프가 5전3선승제로 바뀐 2008년 이후 하위팀이 상위팀을 두 번이나 꺾고 한국시리즈에 오른 것은 두산이 처음이다. 두산은 마지막 우승이었던 지난 2001년에도 준플레이오프부터 시리즈를 시작해 한국시리즈 우승을 차지한 바 있다. 두산은 마지막 우승 당시 한국시리즈 상대가 삼성이었음을 기억하고 있을 것이다.", "prompt": "두산의 마지막 우승 당시의 한국시리즈 상대는 삼성이었다."}, "output": "6.375"}
{"id": "nikluge-2022-nli-train-000002", "input": {"context": "그렇다. 김대중 전 대통령이 북한과 협상할 때 강조하는 '모개흥정'과 닮은꼴이다. 그러나 그가 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈음을 기억하면 별로 새삼스러운 일도 아니다.", "prompt": "그는 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈다."}, "output": "6.75"}
{"id": "nikluge-2022-nli-train-000003", "input": {"context": "니콜라 사르코지 프랑스 대통령은 지난해 중국으로부터 228억달러의 투자를 유치하기 위해 “프랑스는 다른 문화를 존중한다”고 말했다. 이는 중국이 인권문제로 선진국들로부터 비판받을 때마다 내세웠던 논리다. 사르코지가 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있음을 기억하면 중국의 경제적 힘에 체면을 버린 셈이다.", "prompt": "사르코지는 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있다."}, "output": "6.375"}
```


pred_data
```
{"id": "nikluge-2022-nli-train-000001", "input": {"context": "준플레이오프가 5전3선승제로 바뀐 2008년 이후 하위팀이 상위팀을 두 번이나 꺾고 한국시리즈에 오른 것은 두산이 처음이다. 두산은 마지막 우승이었던 지난 2001년에도 준플레이오프부터 시리즈를 시작해 한국시리즈 우승을 차지한 바 있다. 두산은 마지막 우승 당시 한국시리즈 상대가 삼성이었음을 기억하고 있을 것이다.", "prompt": "두산의 마지막 우승 당시의 한국시리즈 상대는 삼성이었다."}, "output": "6.5732"}
{"id": "nikluge-2022-nli-train-000002", "input": {"context": "그렇다. 김대중 전 대통령이 북한과 협상할 때 강조하는 '모개흥정'과 닮은꼴이다. 그러나 그가 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈음을 기억하면 별로 새삼스러운 일도 아니다.", "prompt": "그는 정치인 김대중의 비서 출신이고 국민의 정부에서 청와대 정무비서관과 국정상황실장을 지냈다."}, "output": "6.9801"}
{"id": "nikluge-2022-nli-train-000003", "input": {"context": "니콜라 사르코지 프랑스 대통령은 지난해 중국으로부터 228억달러의 투자를 유치하기 위해 “프랑스는 다른 문화를 존중한다”고 말했다. 이는 중국이 인권문제로 선진국들로부터 비판받을 때마다 내세웠던 논리다. 사르코지가 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있음을 기억하면 중국의 경제적 힘에 체면을 버린 셈이다.", "prompt": "사르코지는 중국의 티베트 탄압에 대한 항의 표시로 베이징 올림픽 개막식을 보이콧하겠다고 경고한 바 있다."}, "output": "6.3574"}
```

베이스라인의 평가 결과는 아래와 같습니다.
| 모델                       | MSE         |
| ---------------------------- | -------------- |
| xlm-roberta-base  |3.1194 |


## reference
xlm-roberta-base in huggingface (https://huggingface.co/xlm-roberta-base)

국립국어원 인공 지능의 언어 능력 평가 (https://corpus.korean.go.kr/)
## Authors
- 정용빈, Teddysum, ybjeong@teddysum.ai
