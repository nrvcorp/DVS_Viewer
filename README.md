# DVS Viewer

DVS Viewer is a release package for viewing and working with DVS camera data from NRV hardware cameras.

The viewer supports real-time camera streaming, DVS data display, raw data saving, frame image saving, video recording, and playback of saved DVS data.

## Viewer 를 시작하기 전 해야하는 환경 세팅

For Windows environments, see:

- [DVS Viewer for Windows](Windows/x64/README.md)

For Linux environments, see:

- [DVS Viewer for Linux](Linux/x64/README.md)

## 뷰어 시작하기 

뷰어에는 여러가지 종류의 기능이 있습니다. 

1. 카메라를 연결하고 실시간 데이터 스트리밍을 볼수있습니다

2. 가공되지 않는 기본 데이터 저장 기능 및 가공되어 프레임 기반으로 나오는 이미지 및 영상 저장 기능

3. 저장된 데이터를 재생시켜 이벤트 개수, 프레임 레이트, 타임스탬프 등 다양한 디버깅이 가능한 기능

4. 실시간으로 센서의 레지스터 값을 제어하며 변화 양상을 확인할 수 있는 기능

각 번호에 대한 자세한 실행방법을 확인하고 싶으시면 아래를 참고하세요.

### 시작하기에 앞서

하고자하는 실행이 잘 안되는 경우, 아래의 체크박스의 상태를 확인하세요!
엑스가 뜬다면 버튼 동작에 있어서 오류가 난 것입니다. 소프트웨어를 껏다 키거나 카메라를 리셋 시킨 후, 다시 가이드 순서에 따라 진행해 주세요
체크박스에 체크 모양이 뜬다면 정상적으로 진행된것입니다.

혹시라도 동작이 안된다면 연락주세요 : 

### 1. 라이브 스트리밍 방법

라이브 스트리밍을 진행하려면, 먼저 DVS Camera가 USB에 꼽혀 장치가 인식이 되어야 합니다. 
만약 장치 인식이 안된다면, 각 os 별 환경세팅을 먼저 진행해 주세요

라이브 스트리밍을 하기 위한 전체 동작은 다음과 같습니다.

![Device Select](Images/Device_Select.png)

1. Devcie 선택 창에서 사용하고 싶은 장치를 선택하세요

2. 세팅 창에서 각 장치 별로 호환가능한 세팅을 선택하여 