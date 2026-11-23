# 5주차(슈팅게임 이어서, 애니메이션 적용)

```
   **충돌되는데 통과 되는 것 : 
    박스 콜라이더(Box Collider)에서 이스 트리거 체크해주고, 리기드바디에서 Use gravity 체크해제
   
   사운드 입히는 법 : 
   소리가 나야 하는 부분에 Audio Source 컴포넌트 추가 후, Audio Clip에 해당 사운드를 넣는다. 
   ex) 전투기 터지는 소리는 wing에, 
   장애물 맞췄을때 피격 소리 Prefabs ExpHit에, 
   미사일 소리는 Missile 등등
   
   UI Text, TextMeshPro 차이점? : Text는 크기조정 inspector에 가능, MeshPro는 크기조정 Scene에 크기조정 따로 가능 inspector에는 절대적인 크기만 조정함. 
   텍스트의 글꼴 입히는것은 Text는 Aa로 가능 한데 MeshPro는 SDF파일로 해야함. 그래서 
   바꾸는 방법은 Window -> TextMeshPro -> Font Asset Creator 에서 Source Font File에 바꾸려는 폰트 파일 끌어서 입력시키고 세이브 하면 됨. 
   
   UI Button, ButtonMeshPro 차이점 : 
   
   윈도우, 안드로이드 빌드 세팅 차이점 :** 
   
```

![Untitled](Untitled%2025.png)

por은 세로

아래 2개 가로 

![Untitled](Untitled%2026.png)

패키지 네임은 회사 이름 com.도메인. 고유. 등등 하면됨

버전은 내 마음대로 넣으면됨.  번들 버전 코드랑 버전이랑 숫자 같게

![Untitled](Untitled%2027.png)

API버전은 최소 버전으로 잡으면 좋음(호환 범위 높이는 것)  

vr은 7.0?부터 가능? 확인해봐야함

![Untitled](Untitled%2028.png)

Asset에 있는 Art-Model을 끌어서 히라키에 놓으면 모델 생성이 되고, Ani폴더에 애니메이터 컨트롤러 생성 후 Player에 생성한걸 넣는다. 

![Untitled](Untitled%2029.png)

이제 저 객체에 행동을 취하려면 

![Untitled](Untitled%2030.png)

밑줄 친 부분 끌어다 쓰거나 밑줄 칠한게 하나 밖에 없으면 위에 박스채로 옮겨도 됨.

![Untitled](Untitled%2031.png)

Make Transition : 이 행동을 취하고 나서 다음으로 취하게 하는 것.

Set as Layer Default State : 디폴트로 행동을 취하게 하는 것.

