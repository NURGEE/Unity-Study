# 유니티2 다른 강사님(9월 탱크게임 오적군 탱크 생성 및 동작, AI, 유도 미사일 구현 9 / 22 9/15일은 추석)

```
적군 탱크 만들기 
프리팹에 불릿과 파티클이 있는데 그곳에 현재 만들어 놓은 탱크를 카피 후 하이라키에 놓는다. 그리고 이름을 enemy로 바꾸고 탱크 컨트롤러 스크립트를 지운다. 
그리고 AI Controller 스크립트 생성해서 넣기.

이 스크립트에
public GameObject topCannonRoot = null;
public GameObject top = null;
pulbic GameObject cannonRoot = null;
pulbic GameObject wheel01 = null;
pulbic GameObject wheel02 = null;
pulbic GameObject wheel03 = null;
pulbic GameObject wheel04 = null;
public GameObject pointLightObject = null;

public float speed = 5;
public float rotSpeed = 120;
public float rotSpeedTop = 200;
public float rotSpeedCannon = 200;

public float bullet_power = 600;

bool bCannoMov = false;

public GameObject bullet;
public GameObject muzzle;

public GameObject fragExplode;

public GameObject playerPos;
public GameObject movPos;

float curHp = 500f;
float maxHp = 500f;

enum _AI_STATE_
{
	_NONE_,
	_MOVE_TO_WAY_POINT_,
	_ATTACK_,
	_IDLE_,
}

_AI_STATE_ aiState;

// 공격 제어, 일정 시간마다 공격(포격 쿨)
float actAttack = 0;

Start에 
aiState = _AI_STATE_._MOVE_TO_WAY_POINT_;

Update에
	float amtToMove = speed * Time.deltaTime;       // 거리
	float amtToRot = rotSpeed * Time.deltaTime;     // 회전
  float amtToRotTop = rotSpeed * Time.deltaTime;
  float amtToRotCannon = rotSpeedCannon * Time.deltaTime;
  
  switch(aiState)
  {
	  case _AI_STATE_._MOVE_TO_WAY_POINT_;
		  {
			  Vector3 vDir = movPos.transform.position - this.transform.position;
			  float fDistance = vDir.magnitude;
			  
			  if(fDistance <= 1.0f) // 도착
				  aiState = _AI_STATE_._ATTACK_; // 공격 모드
	
	//way point 쪽으로 서서히 회전
			  Quaternion targetRot = Quaternion.LookRotation(vDir.normalized);
			  transform.rotation = Quaternion.Lerp(transform.rotation, targetRot, Time.deltatime * 2.0f);
	
  // 탱크 전후진
			  transform.Translate(Vector3.forward * amtToMove);
	// 바퀴 회전
				wheel01.transform.localRotation *= Quternion.Euler(amtToRot, 0, 0);
				wheel02.transform.localRotation *= Quternion.Euler(amtToRot, 0, 0);
				wheel03.transform.localRotation *= Quternion.Euler(amtToRot, 0, 0);
				wheel04.transform.localRotation *= Quternion.Euler(amtToRot, 0, 0);
		  }
		  break;
		  
	case _AI_STATE_._ATTACK_:
			{
				Vector3 vDir = playerPos.transform.position - this.transform.position;
				float fDistance = vDir.magnitude;
				
				// 서서히 회전 player 바라보기
				Quaternion targetRot = Quaternion.LookRatation(vDir.normalized);
				transform.rotation = Quaternion.Lerp(transform.rotation, targetRot, Time.deltaTime * 3.0f);
				
				//포탄 발사하기
				Vector3 vDeltaRot = transform.rotation.eulerAngles - targetRot.eulerAngles;
				if(vDeltaRot.magnitude < 0.3f)
				{
					//bullet
					var myBullet = Instantiate(bullet, muzzle.transform.transform.position, Quaternion.identity);
					// = objectPool.getBullet();
					
					if(myBullet == null)
							return;
					
					myBullet.SetAcive(true);
					myBullet.transform.position = muzzle.transform.position + 2 * muzzle.transform.forward;
					myBullet.transform.rotation = muzzle.transform.rotation;
					
					// 거리에 따라 파워 다르게
					bullet_power = 1000f * fDistance / 37.0f;
					bullet_power = Mathf.Max(300, bullet_power) // 최저 300이상
					
					Rigidbody rb = myBullet.GetComponent<Rigidbody>();
					rb.Addforce(myBullet.transform.forward * bullet_power);
					
					//light on, off
					Light pointLight = pointLightObject.transform.GetComponent<Light>();
					pointLight.intensity = 3;
					
					StartCoroutine("turnOffLight");
					
					aiState = _AI_STATE_._IDLE_;
				}
									
			}
			break;
  case _AI_STATE_._IDLE_:
		  {
			  actAttack += Time.delatTime;
			  if(actAttact > 3.0f)
			  {
				  actAttack = 0;
				  aiState = _AI_STATE_.ATTACK_; // 쿨 돌게 하고 다시 공격
			  }
		  }
		  break;
	 }
	 코드 추가하고 스크립트에 있는 것(public)들 다 알맞게 넣어주기
		 PlaerPos는 내가 조종하는 탱크 넣어주고
		 MovPos에는 다음 챕터에 있는 create Empty 생성했던 것 중 하나(wayPoint) 넣어주기
		 

그리고 Update 다음 문단에 
IEnumerator turnOffLight()
{
	Light pointLight = pointLightObject.transform.GetComponent<Light>();
	for(float i = 3f; i >= 0f; i -= 0.1f)
	{
		pointLight.intensity = i;
		yield return null;
	}
}

지형지물 생성하기 
큐브 생성후 스케일 조정하고 놓기.

엠티 크레이트 해서 두개 놓기(wayPoint1, 2)

이렇게 완성된 적기 탱크를 프리펩에 끌어다 저장하고, 다시 꺼내서 또 다른 적기 탱크 생성하기. 그리고 wayPoint2 로 바꿔주기 

유도 미사일? 만들기
큐브 생성 후 missile로 변경 스케일 조정 후 박스 콜라이더 있는 지 확인 없으면 추가(충돌 체크)하고 rigidbody 컴포넌트 추가 하고 중력 해제 하기  

유도 미사일 날라가는 이펙트 추가하기
missile에 create Empty 생성 후 particleNode로 이름 짓기
파티클 시스템 컴포넌트 추가하기
Duration -> 지속 시간 조정
그외 조정할거 하기

그리고 스크립트(homingMissile) 생성하기
float time = 0;

public ParticleSystem bulletParticle;

public GameObject targetObj;

Rigidbody rb;

Vector3 vDirToTarget_Pre;

float velocity = 20.0f;
float accel = 15.0f;

private void Awake()
{
	rb = gameObject.GetComponent<Rigidbody>();
}

Start부분에
vDirToTarget_Pre = targetObj.transform.position - transform.position;

Update부분에
Vector3 vDirToTarget;
if(targetObj != null)
	vDirToTarget = targetObj.transform.position - transform.position;
	else
		vDirToTarget = vDirToTarget_Pre;
	
	vDirToTarget_Pre = vDirToTarget;
	
	//목표물에 도착함
	if(vDirToTarget.magnitude < 1.0) return;
	
	//방법 #2
	Vector3 vDirInterpolate = 
	trasnform.position += transform.forward + 0.05f * Time.deltaTime * (vDirToTarget - transform.forward);
	
	tarnsform.LookAt(vDirInterpolate);
	
	// 무조건 앞 방향 이동
	velocity += accel * Time.deltaTime; //가속
	Vector3 vMov = velocity * Time.deltaTime * transform.forward;
	transform.Translate(vMov.x, vMov.y, vMov.z, Space.World);
	
	
	
	
	
	
```

위에 코드 해석

→ 유도 미사일 : 아래 벡터 사진 공식을 이용해서 아래 미사일 공식 적용

![image.png](image%204.png)

벡터를 유도미사일 날아가는 것을 표현하는 방식

![image.png](image%205.png)

Quaternion.Lerp 

-> linear Interpolation

CollisionEnter에서의 Enter의 의미는 최초로 충돌 되면 Enter임.

그러고 유지되다가 아예 통과 돼서 닿는 곳이 없으면 Exit임.

```
다음 위 코드 이어서 
time += Time.deltaTime;
if(time > 10.0f) // 너무 오래 지나면 미사일 소멸
{
	Destroy(this.gameObject);
}
코드 추가 하고 
포탄 터지는 파티클 미사일 파티클에 넣어주고 targetobj는 적기 탱크를 넣어준다. 

Update 다음 문단에 

private void OnCollisionEnter(Collision collision)
{
	ContactPoint pt = collision.GetContact(0); // -> 이렇게 한 이유는 한점만 당해야 충돌체크 한번만 할 수 있음 그렇기에 코드에서의 0은 1번째기 때문에 이렇게 추가함. 
	var myParticle = Instantiate(bulletParticle, pt.point + 2 * Vector3.up, Quternion.identity); // 2 = 약간 위 
		
	if(collision.collider.gameObject.tag == "enemy")
	{
					
	}
		
		
		
		Destroy(this.gameObject); // 충돌 하면 소멸

}	
코드 추가

이렇게 완성된 미사일을 위치를 초기화(0,0,0) 해주고 프리팹에 넣고, 하이라키는 지운다. 

이제 따로 만든 미사일을 자신의 탱크에서 쏠수 있게끔 만드는 방법

탱크플레이어(자신의 탱크)를 프리팹에 넣는다. 

자신의 탱크 스크립트 들어가서 코드 추가

선언부에 

public timePassedMissile = 0; // 기관총 마냥 못쏘게끔 시간 체크 하는 변수

bullet(Fire1) 조건문 아래에 코드 추가 

//missile
timePassedMissile += Time.deltaTime;

if(timePassedMissile > 1.2f && Input.GetButtonDown("Fire2"))
{
	timePassedMissile = 0;
	
	//missile
	GameObject[] enemyTanks = GameObject.FindGameObjectWithTag("enemy"); // 현재 scene에 있는 탱크들
	if(enemyTanks.Length > 0)
	{
		//bullet
		var myMissile = Instantiate(missile, muzzle.transform.position, Quternion.identity);
		
		if(myMissile == null)
			return;
			
		myBullet.SetActive(true);
		myBullet.transform.position = muzzle.transform.position + 2 * muzzle.transform.forward;
		myBullet.transform.rotation = muzzle.transform.rotation;
		
		myMissile.GetComponent<homingMissile>().targetObj = enemyTanks[0];
		myMissile.SetActive(true);
	
	}
	
}

코드 추가 후 자신 탱크 인스펙터에 보면 미사일 빈거 프리팹에 있는 미사일 끌어다 놓고 실행. 

이상 없으면 이제 적 탱크 체력 생성 시키기
AIController 스크립트의 맨아래로 가서 함수 하나 만들기

public void takeDamage(float amount)
{
	curHP -= amount;
	
	float fBarPos = curHP / maxHP;
	//update HP bar
	//transform.Find("Canvas_HP").transform.Find("ImageHP").GetComponent<Image>().fillAmount = fBarPos;
	
	if(curHP <= 0)
	{
		curHP = 0;
		//var myBullet = Instantiate(fragExplode, muzzle.transform.position, Quaternion.identity);
		
		// 탱크 소멸
		Destroy(this.gameObject);
		
		return;
	}
	
}

이렇게 코드 완성 시킨후 미사일 스크립트로 가서 OnCollisionEnter 부분에서 조건문 안에 있는 주석 해제 

다음으로 bulletController 스크립트에
OnCollisionEnter 함수 pt 변수 아래 조건문 추가

if(collision.collider.gameObject.tag == "enemy")
{
	조건문 var myParticle 변수 부분 조건문에 넣기.
	collision.collider.gameObject.GetComponent<AIController>().takeDamage(100.0f);
}

이제 적 탱크에 체력바 달기 
지금 프리팹 적 탱크 이기 때문에 원본에 들어가서 수정하기
탱크 적기 본체 우클릭 UI Canvas 생성하고, Canvas_HP로 변경
그리고 체력바 이미지 생성하기 UI-> Image 이름은 imageBG로 변경

지금은 디자이너가 없기 때문에 그림판으로 체력바 직접 만들기 
그림판 -> 크기 조정 -> 가로 세로 비율 유지 X 64 64 -> 이제 검은색으로 칠하고 저장(black으로) -> 애셋에 저장된 black 넣고 클릭 -> 텍스쳐 타입을 스프라이트 2D UI 로 변경 -> 이미지BG의 소스 이미지에 black 대입 -> image Type Filled로 변경 -> method는 수평 -> 오리진은 Right로 수정 -> 크기가 너무 크기 때문에 이미지BG 클릭하고 크기 조정 

CanvasHP 클릭 -> Render Mode를 World Space로 변경 -> 위치는 0,0,0 변경 -> 크기 탱크 크기에 알맞게 변경 

이제 체력을 나타내는 빨강색 저장후 스프라이트 타입 변경하고, Canvas_HP에 BG하나 복사 해서 빨간색 넣는다.(ImageHP) 

이렇게 완성된 후 적 탱크 원본을 나가면 탱크 둘다 체력바 생성이 되어있다. 

이제 체력 다는걸 표시하기위해 AIController 스크립트로 들어가서 takeDamage 함수 주석 제거하고 using Unity.Engine.UI 추가 

그리고 이렇게 끝나면 체력이 -로 나올수 있기 때문에 
주석 update HP bar위에 
curHP = Mathf.Max(0, curHP); 
코드 추가 

폭발 파편 만들기(적 탱크 죽었을때 부서진 탱크 표현)
create empty 생성 -> fragTank 이름 변경 
그 안에 큐브 생성 -> top 이름 변경 
실린더도 생성 -> cannon 이름 변경
큐브 생성 -> wheel 이름 변경
해서 부서진 탱크 알맞게 표현 하기(위치 크기 조정) 

대충 만들어서 디자이너한테 요구하기?ㅋㅋ

다음 top에 Rigidbody 컴포넌트 추가
스크립트(addforce) 생성 

선언부에
Rigidbody rb;
float act = 0f;

Start부분에 
rb - this.transform.GetComponent<Rigidbody>();

Update부분에
//힘을 가한다.
if(act == 0)
{
	Vector3 vDir;
	vDir.x = (float)Random.Range(-9, 10);
	vDir.y = (float)Random.Range(0, 20);
	vDir.z = (float)Random.Range(-9, 10);
	rb.AddForce(vDir.normalized * (float)Random.Range(10, 2000));	
}

act += TIme.deltaTime;
if(act > 3.0)
	Destroy(this.gameObject);

코드 완성 후 
top, cannon, wheel에 AddForce 스크립트 추가하고 Rigidbody컴포넌트 추가 되어있는지 확인 후 실행 

이제 fragTank를 프리팹에 넣는다. 그리고 적 탱크 스크립트로 가서 takeDamage 함수 부분에 조건문 주석 해제 
원본 적 탱크 들어가서 추가된 fragtank를 프리팹 끌어서 넣는다. 그리고 실행 
 

```

rag doll

spline manager

싱글턴 패턴

이 두개는 헬기 자동으로 순찰 돌때? 쓰이는 거같음.

 
베지어 곡선은 포인트 찍으면 다 돌지는 않음? 

cat mul rom 은 점들을 찍으면 그것들을 모두 지남

