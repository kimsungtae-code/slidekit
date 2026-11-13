# slidekit

slidekit 은 줄 단위 글로 쓴 장표 명세를 받아 편집 가능한 pptx 파일을 만드는 Python 장표 엔진이다. 배치, 글자 크기, 색, 넘침 점검은 엔진이 맡는다.

- 템플릿 8종(navy, teal, terracotta, forest, graphite, midnight, plum, crimson)
- layout 20종(표지, 목차, 구분, 마무리, 목록, 카드, 핵심 수치, 큰 수치, 비교, 표, 차트, 인용, 단계 흐름, 일정, 순환형, 중심 연결, 분할 원형, 로드맵, 4분면, 계층)
- 아이콘 256개(Tabler Icons, MIT)
- 필요 패키지: python-pptx, Pillow(Python 3.12 에서 사용)

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

== cover

== kpi | 주요 운영 지표
> 문의량은 늘었으나 응답 시간은 길어졌다
- 1.8배 | 월 문의 건수 | 전년 대비
- 26시간 | 평균 첫 응답 시간 | 9시간 증가

== closing | 감사합니다
''')
slidekit.finish("output/개편계획.pptx")
```

출력 첫 줄은 `built <파일> | slides N | warnings M` 이고, 다음 줄부터 고칠 곳이 나온다.

## 파일

- `slidekit.zip`: 엔진 묶음. 풀면 `slidekit/` 폴더가 나온다.
- 아이콘 라이선스는 묶음 안 `slidekit/assets/icons/LICENSE-tabler.txt` 에 있다.
