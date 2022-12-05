### 1단계 - 웹 성능 테스트
#### 경쟁사와의 성능 비교(MOBILE)
| MOBILE | RUNNINGMAP | 카카오맵  | 네이버지도 |
|--------|------------|-------|-------|
| FCP    | 14.7s      | 1.7s  | 2.6s  |
| TTI    | 15.2s      | 4.8s  | 7.2s  |
| SI     | 14.7s      | 7.0s  | 6.0s  |
| TBT    | 480ms      | 80ms  | 960ms |
| LCP    | 15.2s      | 5.5s  | 7.9s  |
| CLS    | 0.042      | 0.005 | 0.03  |
<br/>

#### 경쟁사와의 성능 비교(DESKTOP)
| DESKTOP | RUNNINGMAP | 카카오맵  | 네이버지도 |
|---------|------------|-------|-------|
| FCP     | 2.7s       | 0.5s  | 0.6s  |
| TTI     | 2.8s       | 0.7s  | 1.3s  |
| SI      | 2.7s       | 2.6s  | 2.2s  |
| TBT     | 30ms       | 0ms   | 70ms  |
| LCP     | 2.8s       | 1.2s  | 1.6s  |
| CLS     | 0.004      | 0.039 | 0.006 |
<br/>

#### 측정 항목 키워드(용어) 정리
- FCP(First Contentful Paint): 사용자가 화면에서 첫 번째 콘텐츠(텍스트, 이미지)를 볼 수 시간(지점)
- TTI(Time To Interactive): 웹 페이지가 완전히 상호작용할 수 있는 상태가 될 때까지 걸리는 시간
- SI(Speed Index): 웹 페이지를 불러올 때, 콘텐츠가 시각적으로 표시되는데까지 걸리는 시간
- TBT(Total Blocking Time): 웹 페이지가 사용자 입력에 응답하지 못하도록 차단된 총 시간
  - 메인 스레드가 오랫동안 점유되어 상호작용하지 못하는 시간
- LCP(Largest Contentful Paint): 뷰포트의 컨텐츠 중 가장 큰(넓은) 영역을 차지하는 이미지나 텍스트 요소가 처음 로딩되는 시간
- CLS(Cumulative Layout Shift): 사용자가 예상치 못한 레이아웃 이동을 경험하는 빈도

<br />

1. **웹 성능예산은 어느정도가 적당하다고 생각하시나요**

| *       | FCP  | TTI  | SI   | TBT   | LCP  | CLS  |
|---------|------|------|------|-------|------|------|
| MOBILE  | 2.0s | 5.0s | 6.0s | 200ms | 5.0s | 0.03 | 
| DESKTOP | 0.5s | 0.7s | 2.0s | 30ms  | 1.3s | 0.04 | 

- 경쟁사 중 공기업이 아닌 카카오맵, 네이버지도와 비슷한 수준의 성능 예산이 적당하다고 생각합니다.  
  - 기존 서비스중인 카카오나 네이버와 비슷한 성능 목표를 잡아 개선하지 않는 한 사용자는 저희 서비스를 사용하지 않을 것이라고 생각했습니다.

2. **웹 성능예산을 바탕으로 현재 지하철 노선도 서비스의 서버 목표 응답시간 가설을 세워보세요.**  
웹 성능 예산의 측정 항목 중 LCP, FCP 등 사용자에게 실제 콘텐츠가 보이는 부분에 대해 줄여보기 위해 노력할 것 같습니다.  
그 이유는 3초의 법칙처럼 로딩하는 시간이 느리고 길수록 사용자는 서비스를 사용하지 않고 이탈할 것이라고 생각했기 때문입니다.
따라서 PageSpeed에서 추천하는 아래의 개선방안처럼 점진적으로 개선해 볼 것 같습니다.  
   - 텍스트 압축(/js/vendors.js, /js/main.js)
   - 사용하지 않는 자바스크립트 줄이기(/js/vendors.js, /js/main.js)
   - 정적인 애셋 제공하기(/js/vendors.js, /js/main.js, /images/main_logo.png, /images/logo_small.png)
   - 최대 크기 이미지 미리 로드하기(/images/main_logo.png)
   - 렌더링 차단 리소스 제거하기(css)
   - 이미지 요소에 명시적인 너비 및 높이 설정하기(/images/main_logo.png, /images/logo_small.png)  

추가적으로 현재 경로 검색 API의 요청 응답시간에 대한 퍼포먼스는 아래와 같기에 양호한 상태라고 판단했습니다.
<img width="624" alt="스크린샷 2022-12-02 오전 1 36 16" src="https://user-images.githubusercontent.com/44702580/205108682-44eac533-c817-4935-9c96-5006908e497e.png">

<br/>

---

### 2단계 - 부하 테스트 
1. 부하테스트 전제조건은 어느정도로 설정하셨나요  

**대상 시스템 범위**
- 메인 페이지
- 로그인
- 내 정보
- 경로 페이지
- 경로 조회
<br/>

**부하 테스트 시 저장될 데이터 건수 및 크기**  
조회 데이터(추가되는 데이터는 없음)
- 노선 : 23 건
- 역 : 616 건
- 지하철 구간 : 340 건

**1일 사용자 수(DAU): 51만**
- 네이버 지도 (MAU): 2129만
- 카카오 맵(MAU): 950만
  - 참고: https://www.dailypop.kr/news/articleView.html?idxno=63289
  - 두 경쟁사의 평균 MAU = 1539만, DAU = 51만
<br/>

**피크 시간대 집중률**
- 18-19시(1,308,385명) / 평균 (628,750명) = 2.08
  - https://insfiler.com/detail/rt_subway_time-0003
<br/>

**1명당 1일 평균 접속 수**
- 출근길(1회) + 퇴근길(1회) = 2

**Throughput: 1일 평균 rps ~ 1일 최대 rps**
- 1일 총 접속 수: 102만
  - 1일 사용자 수(DAU, 51만) * 1명당 1일 평균 접속 수(2회)
- 1일 평균 rps: 12
  - 1일 총 접속 수(102만) / 86,400(초/일)
- 1일 최대 rps: 25
  - 1일 평균 rps(12) * 집중률(최대 트래픽 / 평소 트래픽)
<br/>

**latency: 100ms**
<br/>

**VUser**
- T = (5 * 0.2) + 1s(내부망에서 테스트를 진행하기에 latency 추가) = 2
  - R: 5, 메인 -> 로그인 -> 내 정보 -> 경로 페이지 -> 경로 검색
  - http_req_duration: 0.2(200ms)
- VUser = (rps * T) / R
- 평균 VUser = (12 * 2) / 5 = 5명
- 최대 VUser = (25 * 2) / 5 = 10명
<br/>

2. Smoke, Load, Stress 테스트 스크립트와 결과를 공유해주세요

k6 폴더에 정리 해두었습니다.

---

### 3단계 - 로깅, 모니터링
1. 각 서버내 로깅 경로를 알려주세요
- nginx log: /home/ubuntu/nextstep/nginx-log
- application log: /home/ubuntu/nextstep/infra-subway-monitoring/subway.log
- 회원가입, 로그인 event log: /home/ubuntu/nextstep/infra-subway-monitoring/log/file.log
- 경로 찾기 event log: /home/ubuntu/nextstep/infra-subway-monitoring/log/json.log

2. Cloudwatch 대시보드 URL을 알려주세요
- https://ap-northeast-2.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-2#dashboards:name=giraffelim-dashboard
