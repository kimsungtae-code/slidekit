# slidekit

slidekit 은 줄 단위 글로 쓴 장표 명세를 받아 편집 가능한 pptx 파일을 만드는 Python 장표 엔진이다. 배치, 글자 크기, 색, 넘침 점검은 엔진이 맡는다. 현재 판은 0.5.0 이다.

- 템플릿 8종(navy, teal, terracotta, forest, graphite, midnight, plum, crimson)
- layout 24종(표지, 목차, 구분, 마무리, 목록, 카드, 핵심 수치, 큰 수치, 비교, 표, 차트, 인용, 단계 흐름, 일정, 순환형, 중심 연결, 분할 원형, 로드맵, 4분면, 계층, 시퀀스 다이어그램, 흐름도, 구조도, 복합 장표)
- 밀도 설정: 문서 머리의 `density:` 줄(brief, standard, dense)에 따라 장마다 허용하는 항목 수와 도형 수가 달라진다. 생략하면 standard 다.
- 아이콘 256개(Tabler Icons, MIT)
- 필요 패키지: python-pptx, Pillow(Python 3.12 에서 사용)
- 모든 개체는 PowerPoint 에서 고칠 수 있는 도형, 연결선, 표, 차트다.

## 0.5.0 에서 달라진 점

- 복합 장표(split)를 2~4칸으로 넓혔다. `arrange:` 줄로 배치(2, 3, 1+2, 1/2, 1/3, 2x2)를 고르고 `[a 종류] 제목` 부터 `[d 종류] 제목` 줄로 칸을 연다. 칸에는 차트, 표, 목록, 핵심 수치, 흐름도, 시퀀스 다이어그램, 구조도를 넣을 수 있고, 칸 제목 앞에 (a), (b) 표시가 붙는다. 칸 크기에 따라 넣을 수 있는 항목 수가 다르다.
- 도식에 번호 주석을 더했다. `- mark 노드 이름 : 설명` 줄을 쓰면 노드 모서리에 번호를 붙이고 그 설명을 도식 옆이나 아래에 나열한다.
- 흐름도에 담당 구획을 더했다. `lane 구획 이름: 노드, 노드` 줄로 노드를 구획 띠 안에 놓는다.
- 모든 내용 장에 `takeaway: 문장` 줄로 장 아래의 핵심 문장 띠를 둘 수 있다.
- 표에 칸 안 막대(`bar_col:`)와 행 강조(`highlight_row:`)를, 차트에 쌓은 가로 막대(stacked_bar), 막대와 꺾은선의 혼합(combo), 기준선(`ref: 값 | 이름`)을 더했다.
- dense 문서에서 도형과 글이 모두 적은 내용 장에 「내용이 적다」 경고를 낸다.
- 흐름도와 구조도의 노드 글 넘침 판정을 실제 글 영역에 맞췄다. 마름모 노드가 넓고 낮을 때 넘치지 않는데도 경고하던 문제를 고쳤고, 노드가 실제로 좁으면 라벨 자리를 줄여 노드를 넓힌 뒤에만 경고한다.
- 0.4.0 의 명세는 그대로 쓸 수 있다. `[left 종류]`, `[right 종류]` 로 쓴 좌우 두 칸 장표는 이전과 같게 그려진다.

## 0.4.0 에서 달라진 점

- 도식 layout 4종을 더했다.
  - sequence: 참여자와 생명선, 요청과 응답 화살표, 처리 구간 막대, 메모를 그리는 시퀀스 다이어그램
  - flow: 시작과 끝, 처리, 판단, 저장소, 문서 노드와 갈래, 되돌아가는 연결, 묶음을 그리는 흐름도
  - architecture: 가로 띠 계층 안의 구성 요소와 그 사이 연결을 그리는 구조도
  - split: 한 장을 좌우로 나눠 차트, 표, 목록, 핵심 수치, 작은 흐름도 가운데 둘을 짝짓는 복합 장표
- 도식의 연결선은 노드의 접속점에 붙인 PowerPoint 연결선이며, 실선, 파선, 점선의 뜻은 `legend:` 줄로 범례에 적는다.
- `density:` 머리 줄을 더했다. 범위를 넘는 항목 수와 도형 수는 경고로 알린다.
- 기존 20종 layout 의 명세 형식은 0.3.0 과 같다.

## 사용 방법

```python
import io, sys, urllib.request, zipfile
sys.path.insert(0, ".")
data = urllib.request.urlopen("<slidekit.zip 주소>", timeout=20).read()
zipfile.ZipFile(io.BytesIO(data)).extractall(".")
import slidekit

slidekit.write('''
template: navy
title: 고객 문의 처리 체계 개편 계획
date: 2026-11
density: standard

== cover

== kpi | 주요 운영 지표
> 문의량은 늘었으나 응답 시간은 길어졌다
- 1.8배 | 월 문의 건수 | 전년 대비
- 26시간 | 평균 첫 응답 시간 | 9시간 증가

== flow | 문의 처리 흐름
> 정형 문의는 자동 응답으로 바로 끝난다
direction: right
- 접수 [start]
- 분류 [decision]
- 자동 응답
- 상담원 배정
- 종료 [end]
- 접수 -> 분류
- 분류 -> 자동 응답 : 정형
- 분류 -> 상담원 배정 : 비정형
- 자동 응답 -> 종료
- 상담원 배정 -> 종료
- mark 분류 : 문의 유형을 나눈다

== split | 처리 현황
> 정형 문의가 자동 응답으로 끝나 응답 시간이 줄었다
arrange: 1+2
takeaway: 비정형 문의의 상담원 배정 기준을 다음 달에 정한다
[a chart] 월별 문의 건수
chart: column
labels: 1월 | 2월 | 3월
series 문의: 1240 | 1310 | 2080
unit: 건
[b kpi] 처리 지표
- 26시간 | 평균 첫 응답 시간
[c table] 유형별 처리 시간
bar_col: 2
| 유형 | 처리 시간 |
| 정형 | 2시간 |
| 비정형 | 31시간 |

== closing | 감사합니다
''')
slidekit.finish("output/개편계획.pptx")
```

출력 첫 줄은 `built <파일> | slides N | warnings M` 이고, 다음 줄부터 고칠 곳이 나온다.

## 파일

- `slidekit.zip`: 엔진 묶음. 풀면 `slidekit/` 폴더가 나온다.
- 아이콘 라이선스는 묶음 안 `slidekit/assets/icons/LICENSE-tabler.txt` 에 있다.
