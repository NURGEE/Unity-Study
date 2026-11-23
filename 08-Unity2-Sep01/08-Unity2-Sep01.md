# 유니티2 다른 강사님(9월 적 객체 생성 및 동작 구현, 플레이어 체력 생성, 피격과 데미지 주기 애니메이션(RPG) 끝 9/1)

```
적(뮤턴트) 설정 방법
적 객체 파일 다운 받고 끌어다가 씬에 드래그 하면 생성이 되고 그다음으로 애니메이터에 모션들을 집어 넣는다(공격,이동 모션 등) 위에 플레이어 캐릭터처럼 설정 한다.(트리거, 컨디션 생성 등) 컨트롤러(AI)가 없기 때문에 Anim컨트롤러 드래그해서 갔다 놓는다. 
그리고 길찾기를 해야하므로 Nav Mesh Agent 컴포넌트 추가한다. 
다음으로 rigidbody 컴포넌트, 하이라키에 뮤턴트 객체를 펼치면 뮤턴트 메쉬라고 있는데 그것을 클릭해서 거기에 캡슐 콜라이더 컴포넌트를 추가한다. 그리고 콜라이더를 보면 객체 크기랑 비슷하게 설정한다. is Trigger는 체크 한다. 이유는 플레이어랑 충돌 체크 하기 위함.
적무기 하이라키에 검색해서 찾은 다음 거기에 create empty생성해서 피격 시키는 부위 설정한다. 이 오브젝트(empty)에도 스피어 콜라이더 컴포넌트 추가하고, rigidbody도 추가함. 마지막으로 스크립트 추가하고 이 스크립트는 이 무기가 상대를 때렸는지 확인하는 용도로 사용.

무기쪽 스크립트에는 
private void OnTriggerEnter(Collider other)
{
	if(other.gameObject.tag == "Player")
	{
		if(transform.root.GetComponent<mutentCtrl>().isAttacking())
		{
			other.gameObject.GetComponentInParent<playerCtrl>().takeDamage(100);
		}
	}
}
전체적으로 제어하는 스크립트(Controller) 생성한다. 

다시 플레이어 컨트롤러 스크립트로 가서 체력 추가한다.

public float curHP = 3000f;
public float maxHP = 3000f;

public float curMP = 3000f;
public float maxMP = 3000f;

다음으로 takeDamage함수 선언
public void takeDamage(float amount)
{
	curHP -= amount;
	
	float fBarPos = curHP / maxHP;
	//update HP Bar 이 부분은 UI임.
	//HP_Bar.fillAmount = fBarPos;
	
	if(curHP <= 0)
	{
		curHP = 0;
		
		return;
	}
	
} 코드 추가

다시 뮤턴트 컨트롤러 스크립트로 돌아가서 
enum STATE
{
	_NONE_,
	_IDLE_,
	_FOLLOW_,
	_ATTACK_,
	_DEAD_,
};
  
  STATE st = STATE._IDLE_;
  
  bool isDuringAttack = false;
  
  public void isAttacking()
  {
	  return isDuringAttack;
  }
  
  
Transform transComp;
Rigidbody rigidBD;
NavMeshAgent agent;

Animator anim;

float CurHP = 500f;
float MaxHP = 500f;  

Vector3 lookatPos;
Transform playerTransform; // 플레이어와 뮤턴트와이 거리 
Transform canvasTransform;
Transform cameraTransform;

float distanceToPlayer = 1000000;

publc float distFollow = 6; // 쫒아가는 범위 변수(ex. 6보다 크면 쫒아감 -> 스테이트 변경) 
public float attackRange = 2.0f; // 사거리

float actDead = 0; //시간 측정? 죽고나서 이 시간 뒤에 사라지는 변수?

void Start()안에 
anim = GetComponent<Animator>();
agent = GetComponent<NavMeshAgent>();
transComp = GetComponent<Transform>();
rigidBD = GetCoponent<RigidBody>();

playerTransform = GameObject.Find("Player").transform;
코드 추가

Update 안에는
Vector3 vDir = playerTransform.position = transform.position;
distanceToPlayer = vDir.magnitude;

switch (st)
{
		case STATE._FOLLOW_:
		{
			if(distanceToPlayer < attackRange)
			{
				st = STATE._ATTACK_;
				
				agent.ResetPaht();
				agent.velocity = Vector3.zero;
				agent.isStopped = true;
				agent.SetDestination(transform.position);
				
				anim.SetTrigger("attack");
			}			
			else
				agent.SetDestination(playerTransform.position);
		}
		break;
		
		case STATE._IDEL_:
		{
					if(distanceToPlayer < distFollow)
						{
							st = STATE._FOLLOW_;
							
							agent.isStopped = false;
							anim.SetTrigger("run");
						}	
		}
		break;
		
		case STATE._ATTACK_:
		{
			if(distanceToPlayer > attackRange)
			{
				agent.isStopped = false;
				agent.SetDestination(playerTransform.position)
				agent.SetTrigger("run");
				st = STATE._FOLLOW_;
			}
		}
		break;
		
		
}
코드 추가

다시 하이라키에 있는 뮤턴트 칼부분 클릭해서 인스펙터에 태그 추가해서 Enemy로 변경
플레이어는 플레이어 태그로 변경

다음으로 플레이어 피격 설정을 위해 몸통 클릭후 캡슐 콜라이더 컴포넌트 추가한다. 그리고 태그도 player로 설정.

뮤턴트 컨트롤러 스크립트 Update에서 공격중인지 확인하는 조건문
if(st == STATE._ATTACK_)
{
	isDuringAttack = true;
}
else
	isDuringAttack = false;
코드 추가 

이번에는 플레이어가 적에게 데미지 주는 법
데미지 체크 스크립트에 
트리거 함수 내부에 
other.gameObject.GetComponentInParent<MutantCtrl>().takeDamage(100);
코드 추가
그리고 적객체 컨트롤하는 스크립트에 들어가서 takeDamage()함수 추가
public void takeDamage(float amount)
{
	curHP -= amount;
	
	float fBarPos = curHP / maxHP;
	// update HP bar UI부분이라 주석체크
	//transform.Find("Canvas_HP").transform.Find("Image_HP").GetComponent<Image>().fillAmount = fBarPos;
	
	if(curHP <= 0)
	{
		curHP = 0;
		
		return;
	}
	
} 코드 추가

적객체 체력바 달기
하이라키에 UI-> canvas 생성하고 그 안에 image 2개 생성(HP, 배경(BG))
 그림판으로 작게해서 검정, 빨강 이미지 저장 하고 그 두개 클릭해서 프로젝트로 넣는다. 그다음으로 클릭하고 텍스쳐 타입을 스프라이트(UI)로 변경해준다. 그 다음 이미지 BG 인스펙터에 이미지 -> 소스 이미지에 검정을 넣고, 이미지 타입에는 필드로 바꿔준다. 여기서 필드는 채워주는 타입이다. HP에도 이와 같은 방식으로 빨강 넣어준다. 이렇게 완성됐으면 뮤턴트 객체에 이 canvas를 드래그해서 넣어준다. 그리고 canvas의 layer를 디폴트로 바꿔주고, 인스펙터의 canvas에 Render Mode 에 World Space로 바꿔준다. 마지막으로 크기 수정 하고, 위치 조정한다.(canvas, BG, HP) 
	
플레이어 체력바 달기(전체화면)
이것도 캔버스, UI이미지(BG,HP) 생성하고, canvas의 인스펙터 canvas -> render Mode는 screenspace Overlay로 변경(이거는 스크린 UI가 항상 위로 나옴) BG와 HP는 적객체 이미지와 같게 설정한다. 그리고 BG와 HP의 앵커를 우 상단으로 바꿔준다. 

이제 UI는 띄웠으나 피격 또는 공격시 체력바가 감소해야 하는데 이제 이것을 수정해야함.
플레이어 컨트롤러 스크립트 

using UnityEngine.UI;

Image HP_Bar;
Image MP_Bar; 코드 추가 

void Start 안에
HP_Bar = GameObject.Find("Canvas_PlayerHP").transform.Find("Image_HP").GetComponent<Image>();
takeDamage(0); // 데미지를 0으로 놓음 이제 takeDamage 함수로 가서 주석 처리 했던거 해제하기 

이번에는 적 객체 컨트롤러 스크립트로 가서 
using UnityEngine.UI;

void Start 안에 가서 

//HP bar - 값 채우기(아래 Find안에 이름 캔버스 이름 과 일치해야 작동함.)
transform.Find("Canvas_HP").transform.Find("Image_HP").GetComponent<Image>().fillAmount = 1f; 코드 추가 

그리고 Update 로 가서

// HP bar - 카메라를 바라보게하기
//          HP bar가 쳐다볼 위치 = player 위치에서 캔버스 와 동일 높이
lookatPos = cameraTransform.position;
lookatPos.y = canvasTransform.position.y;
canvasTransform.LookAt(lookatPos, Vector3.up); 코드 추가 

Start에는 
canvasTransform = transform.Find("Canvas_HP").transform; 
cameratransform = GameObject.Find("Main Camera").transform; 코드 추가 

if(st == STATE._ATTACK_)
부분 안에 있는 주석 제거하고, takeDamage함수 에서 주석 제거.
하면 끝.

적 객체 죽었을때의 처리

switch문안에 Dead 코드 추가

case STATE._DEAD_:
{
	actDead += Time.deltaTime;
	if(actDead > 3.5f)
		Destroy(this.gameObject);
}
break;
그리고 switch문 위에 죽은건지 확인하는 조건문 추가
// hp = 0, dead
if(st != STATE._DEAD_ && curHP == 0)
{
	anim.SetTrigger("dead");
	st = STATE._DEAD_;
}
적 객체는 끝.

플레이어 체력바 다 소모 시켰을떄 죽는거 구현
플레이어 컨트롤러 스크립트 들어가서
switch문 DEAD 추가

case STATE._DEAD_:
{
	actDead += Time.deltaTime;
	if(actDead > 2.2f)
	{
		GameObject[] enemies = GameObject.FindGameObjectWithTag("Enemy");
		foreach (var enm in enemies)
			enm.transform.parent.GetComponent<MutantCtrl>().returnToIdle();
		
		transform.Find("Main Camera").parent = GameObject.Find("sceneMgr").transform;
		this.gameObject.SetActive(false);
	}
}
break;구현 하고
따로 float actDead = 0; 변수 선언

switch 문 위에 죽었는지 확인하는 조건문 추가
// hp = 0 ,dead
if( st != STATE._DEAD_ && curHP = 0)
{
	anim.SetTrigger("dead");
	st = STATE._DEAD_;
}
그리고
public void returnToIdle()
{
	st = STATE._NONE_;
	anim.SetTrigger("returnToIdle");
} 코드 추가

그러고 하이라키에 가서 create Empty 하나 만들어서 sceneMgr으로 이름 변경

죽는 동작 하려면 애니메이터에 있는 죽는 모션을 추가후 선로 설정 하면됨. 
Any State -> dead 파라미터(trigger), 컨디션 추가

이대로 하면 죽었을떄 디스토리어로 사라지지만 적 객체가 죽은 플레이어를 인지해서 계속 떄리는 걸 방지 할꺼임.

뮤턴트 컨트롤러 애니메이터 any State -> idle 간선 만들고 이 간선 컨디션 추가 해서 리턴투 아이들로 해준다. 

카메라 처리해주는 스크립트 생성?하기
followPlayercam

private Camera mainCamera;
public float zoomSpeed = 50.0f;

public Transform target;          // target 
public float dist = 10.0f;        // 거리
public float height = 5.0f;       // 높이
public float dampRotate = 5.0f;   // 회전 속도

private Transform tr;             // 카메라

Start 에는 
mainCamera = GetComponent<Camera>();
tr = GetComponent<Transform>();

zoom 함수 선언
private void Zoom()
{
	float distance = (float)Input.GetAxis("Mouse ScollWheel") * -1.0f * zoomSpeed;
	if(distance != 0)
	{
			mainCamera.filedOfView += distance;
	}
}

private void LateUpdate()
{
	float cur_Y_Angle = Mathf.LerpAngle(tr.eulerAngles.y, target.eulerAngles.y, dampRotate * Time.deltaTime);
	//Mathf.LerpAngle(float s, float e, float t) = t 시간 동안 s부터 e까지 각도를 변환 시키는 것.
	
	Quaternion rot = Quaternion.Euler(0, cur_Y_Angle, 0);
	
		tr.position = target.position - (rot * Vector3.forward * dist) + ( Vector3.up * height);
		//타겟 위치 - 카메라 위치 = 카메라가 타겟 뒤로 가야 타겟이 보이겠죠?
		
		tr.LookAt(target);
}

그리고 Update안에는 
Zoom(); 코드 추가

이렇게 완성하고 이스크립트를 플레이어의 메인 카메라에 넣는다. 
그리고 인스펙트의 타겟은 플레이어 스크립트를 넣는다. 

Update의 주 목적? 은 애니메이션?

여기서 <u>LateUpdate</u>는 업데이트가 다 움직일거 움직이고 애니메이션 다 끝나고 나서 동작 하는 함수 이다. 

  
```

