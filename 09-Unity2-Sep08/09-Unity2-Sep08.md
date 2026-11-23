# 유니티2 다른 강사님(9월 3인칭 탱크게임 탱크 생성, 이동, 포탄생성, 발사, 스레드, 코루틴, 이펙트 생성, 파티클 시스템, 오브젝트 풀링, yield 9/8)

```
프로젝트 생성 3D로 만들기 

먼저 바닥판 만들기(평지) 
-> 하이라키에 plane 생성 그리고 scale(크기) 조정 

탱크 오브젝트 만들기(하체, 상체, 포신)
그리고 큐브 이런걸로 탱크 모형 만들고 탱크 하체 부분 만들었으면 create empty하나 만들고 탱크의 전체 탱크의 중심이 되는 부분에 create empty를 끌어다 놓는다. 그런 다음 몸체와 바퀴 4개를 드래그 해서 create empty에 끌어서 넣는다. 
 그런 다음 탱크 상체를 만들기 위해서 하체 부분의 게임 오브젝트를 우클릭 -> create empty해서 상체 게임 오브젝트 생성한다. 그리고 하체 오브젝트랑 위치를 같게 하고, 이제 그 안에 상체 부분을 생성해서 만들고 위치시킨다. 
	 그다음 포신을 만들기 위한 게임 오브젝트를 바깥쪽에 생성한다. 그리고 게임 오브젝트가 투명하기 떄문에 보기 힘드므로 인스펙터 바로 아래에 있는 걸 클릭해서 색깔을 입혀준다 그다음 게임 오브젝트가 포신에 겹쳐지게 위치 시킨다. 마지막으로 포신 모양을 게임 오브젝트에 드래그해서 넣는다.
	 
탱크 전체 오브젝트에 박스 콜라이더 컴포넌트 추가 박스 콜라이더의 크기는 탱크 오브젝트와 크기가 비슷하게 맞춘다. 
그리고 중력과 충돌 체크하기위해 Rigidbody 컴포넌트를 추가
그리고 메인 카메라를 보기 좋게 위치 시키고 드래그 해서 탱크에 넣는다 

이제 다 만든 탱크를 프리팹으로 만들고 저장하는 방법은 우클릭 익스포트 패키지 클릭하고 익스포트 누르면 오브젝트를 패키지(컴퓨터 파일?)로 저장할 수 있다. 이걸 이제 배포하면 됨.

땅바닥을 통통 튀게 하는법
프로젝트에  우클릭 피직 매트리얼 누르면 생성되는데 이걸 바운스 조정 후 이걸 드래그 해서 땅바닥이 될 오브젝트에 넣으면 됨.

다음으로 바퀴 이런거 굴러가게 하기위해 탱크 오브젝트에 스크립트 생성한다.

스크립트 첫 생성 후 
탱크 부위별로 퍼블릭 오브젝트 생성해서 null로 초기화 해준다.

public GameObject topCannonRoot = null;
public GameObject top = null;
public GameObject cannonRoot =null;
public GameObject wheel01 = null;
public GameObject wheel02 = null;
public GameObject wheel03 = null;
public GameObject wheel04 = null;
public GameObject pointLightObject = null;

public float speed = 5;
public float rotSpeed = 120;
public float rotSpeedTop = 200;
public float rotSpeedCannon = 200;

bool bCannonMov = false;

float curHP = 1000f;
float maxHP = 1000f; 코드 추가 

그다음 void 업데이트에 대포를 위아래로 움직이는걸 왼 클릭으로 설정하려고 하는데 이 부분을
if(Input.GetMouseButtonDown(0))
	bCannonMov = true;
if(Input.GetMouseButtonUp(0))
	bCannonMov = false; 코드 추가하고

전후진, 좌우 회전 방향 코드 추가하기
float front_Key = Input.GetAxis("Vertical");
float rot_Key = Input.GetAxis("Horizontal");

마우스 입력으로 받는 코드 추가?
float Horizontal_Mouse = Input.GetAxis("Mouse X");
float moveVertical_Mouse = Input.GetAxis("Mouse Y");

float amtToMove = speed * Time.deltaTime; //거리
float amtToRot = rotSpeed * Time.deltaTime; // 회전
float amtToRotTop = rotSpeedTop * Time.deltaTime 
float amtToRotCannon = rotSpeedCannon * Time.deltaTime;

//탱크 전후진
transform.Translate(Vector3.forward * front_Key * amtToMove);

// 탱크 회전
transform.Rotate(0, rot_Key * amtToRot, 0);

// 바퀴 회전
wheel01.trasnform.localRotation *= Quaternion.Euler(front_Key * amtToRot, 0, 0);
wheel02.trasnform.localRotation *= Quaternion.Euler(front_Key * amtToRot, 0, 0);
wheel03.trasnform.localRotation *= Quaternion.Euler(front_Key * amtToRot, 0, 0);
wheel04.trasnform.localRotation *= Quaternion.Euler(front_Key * amtToRot, 0, 0);

여기서 쿼터니언은 *=의 의미는 더하기임 곱하기 X 

이제 다시 유니티로 돌아가서 public에 이름에 맞는 오브젝트를 스크립트에 드래그해서 넣는다.

if(bCannonMov)
{
	// 포탑(top) 회전
	topCannonRoot.transform.localRotation *= Quaternion.Euler(0, Horizontal_Mouse * amtToRotTop, 0);
	
	// 포신(cannonRoot): up, down)
	Quaternion qtDelta = Quaternion.Euler(0,0, moveVertical_Mouse * amtToRotCannon);
	Quaternion qtOld = cannonRoot.transform.localRotation;
	Quaternion qtNow = qtOld * qtDelta;
	
	// 포신 회전 각도 제약 걸기
	float zAngleLocal = qtNow.eulerAngles.z;
	// Debug.LogFormat("{0}", zAngleLocal);
	if(!(zAngleLocal > 20 && zAngleLocal < 340))
		//포신 : 회전
		cannonRoot.transform.localRotation *= Quaternion.Euler(0, 0, moveVertical_Mouse * amtToRotCannon);

}

대포 쏘는 법 
1. 포탄(대포알) 만들기 
	3D 오브젝트 스피어 생성 -> 크기 조정 -> 스피어 콜라이더, 리지드 바디 컴포넌트 추가 -> 스크립트 생성(bulletController)
	
생성된 스크립트에 코드 추가

Rigidbody rb;
float time = 0;

private void Awake()
{
	rb = GetComponent<Rigidbody>();

}

void Start()
{
	time = 0;
}

void Update()
{
	time += Time.deltaTime;
	if(time > 5.0f) // 너무 오래됨
	{
		Destroy(this.gameObject);
		//gameObject.SetActive(false);
	}
	
	private void OncollisionEnter(Collision collision)
	{
		Debug.Log(collision.gameObject.name);
		Destroy(this.gameObject);
	}
}

폴더 프리팹 생성해서 포탄을 프리팹 파일에 넣으면 프리팹이 된다. 그리고 하이라키에 불릿을 제거한다.

포탄을 나가는 곳을 지정해주기 위해서 탱크 포신 부분에 create empty(muzzle)를 생성한다.

muzzle의 위치를 포신 끝부분에 위치하면서 안닿게 한다.

포탄을 나가게 하기 위해서 탱크에 있는 스크립트로 들어가서 선언 부분에 
public GameObject muzzle;
public GameObject missile;
public GameObject bullet; 
public float bullet_power = 600;
코드 추가하고,

Update부분에는 
//bullet 
if(Input.GetButtonDown("First1"))
{
	// bullet
	var myBullet = objectPool.getBullet(); 지금 이 코드는 할수 없기 떄문에
	
	var my Bullet = Instantiate(bullet, muzzle.trasnform.position, Quaternion.identity); 로 대체한다. 
	if(myBullet == null)
		return;
		
	myBullet.SetActive(true);
	myBullet.transform.position = muzzle.trasnform.position + 2 * muzzle.transform.forward;
	myBullet.transform.rotation = muzzle.transform.rotation;
	
	Rigidbody rb = myBullet.GetComponent<Rigidbody>();
	rb.AddForce(myBullet.transform.forward * bullet_power);
	
	// light on, off
	Light pointLight = pointLightObject.transform.GetComponent<Light>();
	pointLight.intensity = 3;
	
	StartCoroutine("turnOffLight"); 

다음으로 사격했을 때 포신 부분에서 빛이 나게 하는 방법 
muzzle에 empty create 생성 후 PointLight로 이름 변경 
그리고 PointLight 인스펙터에 Light 컴포넌트 추가하고 입맛에 맞게 설정 

그리고 이어서 
IEnumerator turnOffLight()
{
	Light pointLight = pointLightObject.transform.GetComponent<Light>();
	for(float i = 3f; i >= 0f; i -= 0.1f)
	{
		pointLight.intensity = i;
		yield return null;
	}
}

다음으로 이펙트 생성하는 법. 
하이라키 탱크 바깥부분에 파티클 시스템 생성하고 Particle로 이름 변경.
그리고 입맛에 맞게 설정하기
그리고 프리팹에 저장 후 하이라키 파티클 제거 
그리고 불릿 스크립트 선언부에 
public ParticleSystem bulletParticle; 추가 하고 프리팹 파티클 드래그해서 넣기
이제 다시 불릿 스크립트에 가서
OnCollision부분에 
ContactPoint pt = collision.GetContact(0);

var myParticle = Instantiate(bulletParticle, pt.point + 2 * Vector3.up, Quaternion.identity); 코드 추가

다음으로 카메라 매끄럽게? 하기 스크립트(followPlaerCam)를 메인카메라에 넣고 탱크 붙이면됨. 

```

스레드

코루틴 light weight

yield 

yield return null : 다음 프레임에 실행 됨. 

Particle 은 조그만 알갱이 입자

파티클 시스템은 입자를 뿜어내는 시스템 

Emitter는 입자를 뿜어내느 위치, 모양

오브젝트 풀링 : 

Instantiate : 프리팹을 동적으로 생성하여 scene에 추가하는 것

프리팹은 하드디스크에 있어서 로딩 속도 느림

프리팹은 텍스쳐가 사용되는 경우가 많은데 용량이 큼

동적 할당,  즉 힙 메모리를 많이 할당, 비디오 메모리도 많이 할당

OS 커널 검증 등, start에서 초기화 코드 돌아감

게임 실행 중 프리팹을 로딩하는 것은 느리고 적합하지 않음.(렉 걸림)

그러므로 실행 중이 아니라 로딩타임에 미리 다 만들어 놓고(로딩해놓고) 꺼내서 사용하기만 하는 것이 유리함. 

서칭 해서 공부해보기 

