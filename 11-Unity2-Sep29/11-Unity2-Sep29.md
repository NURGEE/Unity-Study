# 유니티2 다른 강사님(9월 탱크게임 오브젝트 풀링으로 포탄, 미사일 저장 및 마무리, 2D 게임 9/29)

```
카메라 부드럽게 돌아가게 하기 
3D 캐릭터 완성본 카메라 스트립트 Follow Player 복사해서 탱크 메인 카메라에 붙이기  

프리팹 해제 하기 
프리팹 돼있는 객체 우클릭 -> 프리팹 -> 언팩 컴플리틀리 클릭

머티리얼 로딩 
png 이미지 파일 하드디스크에서 로딩
2048 이미지 3장 (48 메가), 동적 할당(부하)

오브젝트 풀링
create empty -> objectPool 이름 변경 -> 스크립트(Object Pool) 생성 

선언부에
public static objectPool Instance; //singleton

public GameObject bulletPrefab;
public GameObject missilePrefab;

const int numbullets = 20;
const int numMissiles = 5;

GameObject[] bulletPool;
GameObject[] missilePool;

private void Awake()
{
	instance = this;
	
	bulletPool = new GameObject[numBullets];
	missilePool = new GameObject[numMissiles];
	
}

Update아래 문단에

public static GameObject getBullet()
{
	for(int i = 0; i < numBullets; i++)
	{
		if(!Instance.bulletPool[i].activeSelf)
			return Instance.bulletPool[i];
	}
	
	return null;
}

public static GameObject getMissile()
{
	for(int i = 0; i < numMissiles; i++)
	{
		if(!Instance.missilePool[i].activeSelf)
			return Instance.missilePool[i];
	}
	
	return null;
}

void generateInstance()
{
	for(int i = 0; i < numBullets; i++)
	{
		bulletPool[i] = Instantiate(bulletPrefab, new Vector3(0, 0, 0), Quternion.identity);
		bulletPool[i].SetActive(false); //SetActive는 인스펙터에 있는 체크 유/무임.
		bulletPool[i].transform.parent = this.transform;
	}
	
	for(int i = 0; i < numMissiles; i++)
	{
		missilePool[i] = Instantiate(missilePrefab, new Vector3(0, 0, 0), Quternion.identity);
		missilePool[i].SetActive(false);
		missilePool[i].tranform.parent = this.transform;
	}
}

이렇게 해서 오브젝트 풀 퍼블릭 부분(bullet, missile) 프리팹 넣어주고 실행하기 

오류 없으면 이제 TankController 스크립트로 가서 
Fire1과 Fire2 부분에

var myBullet = 이 부분을 지우고 주석 처리했던 오브젝트 풀 주석을 해제해서 넣는다.

여기서 실행하고 오류 나게 되는데 오류 나는 이유는 오브젝트 풀링은 일회성 소모하는 프리팹이아니라 재활용하는 것이기 때문에 불릿, 미사일을 Destroy하면 안됨. 그러므로 
bulletController스크립트로 가서 OnCollisionEnter 부분에 Destroy를 주석 하고 
// active 끄기
gameObject.SetActive(false);
코드 추가

그리고 Update부분에 Destroy 주석하고, 
gameObject.SetActive(false); 
코드 추가 

그리고 AIController 스크립트에 공격하는 부분에서 
bullet 부분 Instantiate부분 지우고 주석처리한 부분을 해제

Start아래에 
private void OnEnable()
{
		time = 0;
		
		//물리를 켠다
		rb.WakeUp();
}

private void OnDisable()
{
		//물리를 꺼준다.
		rb.Sleep();

}

실행하고 이상없는지 확인

TankController스크립트로 가서 
var myMissile = 이부분 지우고 오브젝트 풀 주석해제

homingMissle 스크립트에 
if(time > 10.0f)
부분에서 Destroy 지우고
gameObject.SetActive(false);
코드 추가 

OnCollisionEnter함수에 Dsetroy 지우고
gameObject.SetActive(false); 
코드 추가

그리고 Awake()다음 문단에 

private void OnEnable()
{
		//물리를 켠다
		rb.WakeUp();
}

private void OnDisable()
{
		//물리를 꺼준다.
		rb.Sleep();
		rb.velocity = Vector3.zero;
		
		//변수 초기화
		time = 0;
		velocity = 20.0f // 초기속도 
}

--------------------------------------------------------
탱크 게임 마무리

시간 관계상 최종 버전에는 랙돌이랑 헬기 있으니 따로 공부.

랙돌 : 인형?
joint : 줄줄이 소세지 처럼 엮여있는거? 
사용예시 -> 출렁 다리 같은거 만들때? 

컴포넌트 -> create ragdoll -> 부품들 알맞게 드래그 하면 래그돌 생성

스플라인 매니저 스크립트 : 포인트 들을 찍으면 도는 것

```

오브젝트 풀링 : 미리 객체를 생성해 가지고 있는 것? 

싱글턴 패턴 : 고유한 하나를 가지고 쓰는 방식? 거의 오브젝트 풀링 할때 이 패턴을 주로 씀.

→ 스태틱 인스턴스 생성?

조건 : 일반화된 싱글턴 패턴은 인스턴스 생성 후 조건문으로 null 일때와 아닐때를 구분해서 함. null이 아니면 이미 그 인스턴스에 값이 존재 하므로 인스턴스 리턴하면 됨.

ex) 

if(instance == null)

instance = this;

else 

return instance; 

```
2D 게임 

유니티 2D 빌트인으로 새로 만들고 
실습 에셋 스토어 Sunny Land 다운 받아서 임포트 

메인 카메라에서 카메라 컴포넌트 사이즈는 위아래로 크기를 잼.

이미지에서
pixels per unit -> 48로 변경 
변경하여 보이는 크기 바꿈

order in layer -> 이걸 통해서 오브젝트가 캐릭터 가리는걸 방지함.
보여지는 우선 순위

다운받은 에셋들을 바닥, 집 이런거 꾸미기 

캐릭터 애니메이션 넣는법
캐릭터 넣을때 스프라이츠 -> 플레이어 -> idle -> 캐릭터 애니메이션 4개 클릭해서 크기 조정(48)하고 씬에 넣으면 저장되면서 쓸 수 있음 

이렇게 생성된 얘를 이름 바꿔주고(player) rigidbody 2D 컴포넌트, Box Collider 2D 컴포넌트 추가

컨스트래인츠 : 2D이기 때문에 z축 못가게 체크 하기  

```

![image.png](image%206.png)

