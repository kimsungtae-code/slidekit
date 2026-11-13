# slidekit

slidekit 은 줄 단위 글로 쓴 장표 명세를 받아 편집 가능한 pptx 파일을 만드는 Python 장표 엔진이다. 배치, 글자 크기, 색, 넘침 점검은 엔진이 맡는다. 현재 판은 0.4.0 이다.

- 템플릿 8종(navy, teal, terracotta, forest, graphite, midnight, plum, crimson)
- layout 24종(표지, 목차, 구분, 마무리, 목록, 카드, 핵심 수치, 큰 수치, 비교, 표, 차트, 인용, 단계 흐름, 일정, 순환형, 중심 연결, 분할 원형, 로드맵, 4분면, 계층, 시퀀스 다이어그램, 흐름도, 구조도, 좌우 분할)
- 밀도 설정: 문서 머리의 `density:` 줄(brief, standard, dense)에 따라 장마다 허용하는 항목 수와 도형 수가 달라진다. 생략하면 standard 다.
- 아이콘 256개(Tabler Icons, MIT)
- 필요 패키지: python-pptx, Pillow(Python 3.12 에서 사용)

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

== closing | 감사합니다
''')
slidekit.finish("output/개편계획.pptx")
```

출력 첫 줄은 `built <파일> | slides N | warnings M` 이고, 다음 줄부터 고칠 곳이 나온다.

## 파일

- `slidekit.zip`: 엔진 묶음. 풀면 `slidekit/` 폴더가 나온다.
- 아이콘 라이선스는 묶음 안 `slidekit/assets/icons/LICENSE-tabler.txt` 에 있다.
