# 4.15 Regularization, learning rate 차이점
  - https://stats.stackexchange.com/questions/168666/boosting-why-is-the-learning-rate-called-a-regularization-parameter
# 5.27 CRAFT- test.py open-cv issue (Segmentation fault (core dumped))
    cv2.connectedComponentsWithStats(text_score_comb.astype(np.uint8),connectivity=4) -> remove 'connectivity' parameter 
    
    maybe version issue
  - https://github.com/opencv/opencv-python/issues/602#issuecomment-1070290110

# 6.14 Matplotlib error
  - 에러로그 

```
s@js:~/PnID$ /bin/python3 /home/js/PnID/test2.py
QObject::moveToThread: Current thread (0x4829cd0) is not the object's thread (0x4b960a0).
Cannot move to target thread (0x4829cd0)
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "/home/js/.local/lib/python3.8/site-packages/cv2/qt/plugins" even though it was found.
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.
Available platform plugins are: xcb, eglfs, linuxfb, minimal, minimalegl, offscreen, vnc, wayland-egl, wayland, wayland-xcomposite-egl, wayland-xcomposite-glx, webgl.
Aborted (core dumped)
```
  
  - 발생원인: 일단 잘 모르겠음, anaconda3 가상환경 써보겠다고 설치한 다음 opencv로 불러온 이미지를 확인하기 위해 matplot 코드 사용시 위와같은 증상 발생 
  - 처음에는 opencv 문제로 예상, 에러로그로 웹에서 뒤져봤을 때, opencv 버전을 낮춰서 해결한 사례가 있었음 -> 해결불가 

  - 해결: matplotlib을 3.5.0버전으로 낮추니 정상적으로 실행되는듯 
  - 원인: pyqt로 동작하는 matplotlib가 qt관련 플러그인을 못찾음 -> 왜 그런지는 모름(cv2, anaconda 설치과정에 무언가 꼬였을 가능성)
  
  ```
  pip uninstall matplotlib
  
  pip install matplotlib==3.5.0
  ```
  
 # 6.17 Matplot image output issue
  - 발생경위:
  - BGR 이미지를 cv.imread()로 읽어서 RGB로 변환한 이미지를 cvtColor()로 그레이 스케일로 변환함, 그 상태에서 matplot으로 이미지 출력 시 흑백이미지로 표시되지 않는 문제가 발생
  
  - 잘못 출력된 이미지 
  ![Wrong_img](https://user-images.githubusercontent.com/32115744/174228762-2d439768-5ad6-4f8b-bfaa-0ba11be55255.png)
