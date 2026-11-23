# 유니티2 다른 강사님(10월 2D 게임, RPG 게임 10/06)

```
씬에 띄울것들 다른 오브젝트들 코인이런거 애니메이션 화 해서 씬에 띄우기

캐릭터 모션들 다 사용하는 방법.
 
idle 일때, running 일때, 이런 모션들 애니메이션화 시키기 
그렇게 되면 모든 행동 할때 매니저, 애니메이션이 생기는데 여기서 주축이 될 idle 하나만 매니저 지우지말고 나머지 지우기.(애니메이션은 써야하기 때문에 삭제 X)

그러고 매니저 더블클릭하면 애니메이터 가 뜨는데 거기에 쓰고 싶은 애니메이션 넣기. 

그리고 idle 상태였다가 러닝을 하기 때문에 idle이랑 러닝 애니메이션 간선 연결 하기 
idle -> running 간선에 트리거 형태 파라미터(run) 생성
그리고 트리거 형태 파라미터 jump, idle 생성 

점프 애니메이션도 위의 방식으로 
idle -> 점프 간선 과 뛰면서 점프 할수 있으니 러닝 -> 점프 간선 추가 

그리고 컨디션 각자에 맞는 파라미터 선택.

이제 player에 스크립트 추가 

선언부
Rigidbody2D rigid;
Animator animator;

public float jumpPower = 3f;
public float movePower = 1f;

float actWait = 0;

enum _state_
{
	_NONE_,
	_IDLE_,
	_RUN_,
	_WAIT_JUMP_,
	_JUMP_,
}

_state_ st = _state_._IDLE_;

Start 부분

rigid = gameObject.GetComponent<Rigidbody2D>();
animator = gameObject.GetComponentInChildren<Animator>();

Update부분
float inputHoriz = Input.GetAxisRaw("Horizontal");
bool isJumpPressed = Input.GetButtonDown("Jump");

bool isMovPressed = (inputHoriz != 0);
bool isMovNotPressed = (inputHoriz == 0);

switch(st)
{
	case _state_._IDLE_:
	{
		if(isMovPressed)
		{
			animator.SetTrigger("run");
			st = _state_._RUN_;
		}
		
		if(isJumpPressed)
		{
			//Jump();
			st = _state_._WAIT_JUMP_;
		}
		
	}
	break;
	case _state_._RUN_:
	{
		if(isMovNotPressed)
		{
			st = _state_._IDLE_;
			animator.SetTrigger("idle");
			break;
		}	
		if(isJumpPressed)
		{
			Jump();
			animator.SetTrigger("jump");
			st = _state_._WAIT_JUMP_;
			break;
		}
		
		Mov(inputHoriz);
	}
	break;
	case _state_._WAIT_JUMP_:
	{
		actWait += Time.deltaTime;
		if(actWait > 1.0f)
		{
			actWait = 0;
			st = _state_._JUMP_; // 좋은 구현은 아닌거 같음 올라가는 길목일때? 지금 상황에서는 맞는 거라 괜찮음
		}	
		
		if(isMovPressed)
			Mov(inputHoriz);
		
		if(isJumpPressed)
			Jump();
		
	}
	break;
	case _state_._JUMP_:
	{
			RaycastHit2D hit = Physics2D.Raycast(transform.position, Vector2.down, 0.5f);
			Debug.DrawRay(transform.position, Vector2.down * 0.5f, Color.black);
	
			if(hit)
			{
				animator.SetTrigger("idle");
				st = _state_._IDLE_;
			}
			
			if(isMovPressed) // 점프 하면서 움직일수 있음 
				Mov(inputHoriz);
			
			if(isJumpPressed) // 여러번 점프 할수 있음 
				Jump();
			
	}
	break;
}

Update아래 문단에 함수 추가

private void Mov(float inputHoriz)
{
	Vector3 moveVelocity = Vector3.zero;
	
	if(inputHoriz < 0)
	{
		moveVelocity = Vector3.left;
		transform.localScale = new Vector3(-1, 1, 1); //왼쪽 바라봄
	}
	else if(inputHoriz > 0)
	{
		moveVelocity = Vector3.right;
		transform.localScale = new Vector3(1, 1, 1); //오른쪽 바라봄
	}
	transform.position += moveVelocity * movePower * Time.deltatime;
}

void Jump()
{
	animator.SetTrigger("jump"); //점프 애니메이션
	
	rigid.velocity = Vector2.zero;
	
	Vector2 jumpVelocity = new Vector2(0, jumpPower);
	rigid.AddForce(jumpVelocity, ForceMode2D.Impulse);
}

이렇게 코드 완성 후 실행 
오류없이 실행 되면 점프 했을 때 모션이 너무 빠르기 때문에

이제 보석(코인) 류 먹었을때의 처리

보석(코인) 객체 눌러서 Circle Collider 2D 컴포넌트 추가 
istrigger 체크하기 안하면 통과 못함 

그리고 태그 추가하기
(diamond)로 변경 

다른 먹을만한 것들도 알맞게 태그 추가해서 넣어주기(콜라이더 등) -> 체리 도 추가

다시 플레이어 컨트롤러 스크립트 들어가서 
void Jump함수 아래에 코드추가

private void OnTriggerEnter2D(Collider2D collision)
{
	if(collision.transform.gameObject.tag == "cherry")
	{
		Debug.Log("cherry hit...");
		Destroy(collision.gameObject);
	}	
	else if (collsion.transform.gameObject.tag == "diamond")
	{
		Debug.Log("diamond hit...");
		Destroy(collision.gameObject);
	}
}
 
 
 몬스터(개구리) 생성하기
 
 맨처음 객체 애니메이션 생성 할때처럼 해놓고 씬에 내놓기
 처음 만들었을 때 인스펙터에 컨트롤러가 미싱으로 되어있으면 애니메이션 생성할때 나오는 메니저를 끌어다 놓는다. 
 
 그리고 rigidbody 2D, Box Collider 2D, 스크립트(frog AI) 추가
 
 애니메이터에 모션들 끌어다 놓고 
 처음에 엔트리를 idle에 가도록 설정 하고 
 점프 끌어다 놓고 idle이랑 간선 연결하기.
 파라미터는 
 트리거 형태로 idle, jump 생성
 컨디션 알맞게 설정
 
 스크립트에 
 enum _state_
 {
	 _IDLE_,
	 _JUMP_,
 }
 -state_ st = _state_._IDLE_;
 
 float act = 0;
 
 Animator anim;
 RigidBody2D rigid;
 
 Start 부분에
 
 anim = GetComponent<Animator>();
 rigid = GetComponent<Rigidbody2D>();
 
 Update 부분에
 switch(st)
 {
	 case _state_._IDLE_:
	 {
		 act += Time.deltaTime;
		 if( act > 1.0f)
		 {
			 act = 0;
			 anim.SetTrigger("jump");
			 
			 int rv = Random.Range(0, 2);
			 
			 // limit x position, within 2.0f ~ 8.0f
			 if(transform.position.x > 8.0f)
				 rv = 0;
			 if(transform.position.x < 2.0f)
				 rv = 1;
			 
			 
			 Vector2 v1;
			 if(rv == 0)
			 {
				 v1 = new Vector2(-1, 5);
				 transform.localScale = new Vector3(1, 1, 1); // 왼쪽 바라봄
			 }
			 else
			 {
				 v1 = new Vector2(1, 5);
				 transform.localScale = new Vector3(-1, 1, 1); // 오른쪽 바라봄 
			 }
			 
			 rigid.AddForce(0.5f * v1, ForceMode2D.Impulse);
			 
			 st = _state_._JUMP_;
		 }
	 } 
	 break;
	 
	 case _state_._JUMP_:
	 {
		 act += Time.deltaTime;
		 if(act > 1.0f)
		 {
			 act = 0;
			 anim.SetTrigger("idle");
			 st = _state_._IDLE_;
		 }
	 }
	 break;
 }
 
 
카메라 주인공 따라가게 하기 
두가지 방법이 있는데 하나는 직접 짜서 하는 것과 다른하나는 씬에 머신? 이 있음 

첫 번째 방법으로 하면 메인카메라에 스크립트(cameraController) 추가

public GameObject player; //A라는 GameObject 변수 선언
Transform AT;

Start부분에
AT = player.transform;

Update아래 문단에 
private void LateUpdate()
{
	transform.position = new Vector3(AT.position.x, AT.position.y, -10);
}
 이렇게 완성되고 실행 하면 카메라가 따라가지만 이렇게 코드 짜면 일반 게임에서 쓸수가 없음(구림)
 
두번째 방법으로 패키지 매니저에서 패키지 유니티 레지스트리 Cinemachine 다운받아 사용

다운완료되면 Cinemachine 메뉴가 상단에 추가됨. 
Chinemachine -> virtual camera 클릭
클릭하면 메인 카메라에 빨간 톱니바와 CM vcam생성됨 
CM vcam에 카메라 컴포넌트 follow가 빈칸인데 거기에 플레이어 넣기 

이렇게 되면 플레이어의 위치에 따라 카메라가 따라감
하지만 절벽에 떨어졌을때 카메라가 끝까지 따라가기 때문에 떨어졌을떄 카메라는 위에 남고 플레이어만 떨어지도록 설정할꺼임

Cm vcam -> Add Extention -> 씨네마신 컨파이너클릭 -> 하이라키에 create Empty(confine) -> Polygon Collider 2D 컴포넌트 생성 -> 5각형이 나오는데 점들을 땡겨서 맵 크기를 덮는크기 조정하기 -> 폴리곤 콜라이더의 이즈 트리거 체크(체크 안하면 충돌체크 돼서 모든 객체들 팅겨남)
이렇게 하면 캐릭터는 자유롭게 움직이되 카메라는 폴리곤 콜라이더 안에서만 이동하게 됨. 

이번에는 캐릭터가 떨어지면 죽을수 있도록 설정 하는 방법
create empty(triggerGameOver) -> 박스 콜라이더 컴포넌트 추가-> 죽는 범위 크기 까지 조정 -> 트리거 체크 -> 태그 GameOver 추가 -> player 스크립트로 가서 코드 추가

else if조건문 아래 
else if(collision.transform.gameObject.tag == "GameOver")
{
	Debug.Log("GameOver !");
	GameObject.Find("Canvas").transform.Find("Text").gameObje.SetActive(true);
	this.gameObject.SetActive(false);
}

게임 오버 UI 띄우기
하이라키에 UI -> Canvas -> Canvas 우클릭 텍스트 -> 컴포넌트 부분에 Text Game Over 변경 과 색상 조절 -> 위 코드에 셋액티브 트루로 되어있기 때문에 텍스트를 체크 해제 한다.

--------------------------------------------------------2D 끝

외전 - 
tilemap 2D 공부
isometric tile - 3D 같은 2D 타일임 메이플2같은 느낌

3DChar 옛날에 하던 완성본 꺼내서 공부하기
인벤토리 설명 
유니티 이미지 마우스 따라 검색
스크립터블오브젝트유니티
 
 
```

Debug.DrawRay(transform.position, Vector2.down * 0.5f, Color.black);

레이캐스트 

![image.png](image%207.png)

![image.png](image%208.png)

올라갈 때, 내려갈 때

광선 쏘고 안쏘고

wait jump , jump

파랑선은 착지 한계선

보통 카메라는 LateUpdate에 주로 하고, 물리 연산 이런거는 Update에 주로 코드 작성함.

업데이트가 끝나야 래이트 업데이트가 써짐.

fixed Update도 있음 따로 공부

```
--------------------------------------------------------다른 강사님 유니티 끝
```