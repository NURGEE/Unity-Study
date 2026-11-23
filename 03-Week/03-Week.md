# 3주차(객체지향프로그램, 모델 씌우기, 시점, 이동, 총알 발사(물리 엔진),  )

![Untitled](Untitled%204.png)

**에셋스토에서 받은 모델 원본(보통 정육면체로 되어있음)**

**을 Hierarchy에 끌고가서 바바리안 생성**

![Untitled](Untitled%205.png)

**model : 모델의 크기 이런거 변형 가능**

**Rig : 뼈대?**

**Animation : 이 모델이 행동 가능하는걸 미리 볼 수 있음.**

 **Hierarchy에 생성된 바바리안을 script에 C#파일을 끌어다 컴포넌트 추가해서 바바리안에게 행동을 할수 있게 끔 코드 구현할 수 있음.** 

첫 번째 몬스터 C#파일

```

**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Monster : MonoBehaviour
{
    // Start is called before the first frame update

    // <u>enum :  유한상태기계?의 기초 단계, 열거형 키워드 enum</u>

    // 몬스터 상위 클래스 설정 (원형 클래스, 슈퍼 클래스등으로 불림)
	    -> 공통 부분을 여러번 쓰지 않게 하기 위해 묶는 것?
    // 몬스터들이 공통적으로 들어갈 수 있는 요소들을 설계
    // 메모리 영역에 있는 거에 스크립트 를 넣어서 하는거 : 동적 스크립트 기법

    public enum SORT
    {
        
        HUMAN = 0,
        PLANT, //처음 초기화 하면 자동으로 다음 번호로 되기 떄문에 생략가능
        DEMON,
        UNDEAD

    }

    public SORT sort = SORT.HUMAN;

    public string name = "";
    public int hp = 0;
    public string address = "";
    public bool isBoss = false;
    public bool isFirstAttack = false;
    public bool isCall = false;

    public Monster() // 기본 생성자(원래 생략 되어있음)
    {

    }

    public Monster(string name) 
    // 보통 생략된 생성자를 만들때 매개변수를 추가해서 만듬 매개변수 여러개 해서 여러개의 매개변수 클래스 생성 가능
    {
        this.name = name;
    }

    public Monster(string name, int hp)
    {
        this.name = name;
        this.hp = hp;
    }

    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}**

```

**두 번째 create empty를 생성해 GameManager로 이름 변경** 

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    //객체 인스턴스화 : new라는 키워드를 활용해서 메모리에 할당 

    Monster oak;
    Monster goblin;
    Monster ogre;

    //싱글턴 패턴?
    public static GameManager instance; //이렇게 하면 

    private void Awake() // Start보다 먼저 동작됨 이것 또한 최초 1회 동작
    {
        instance = this;
    }

    // Start is called before the first frame update
    void Start()
    {
       //OakProfile();
        //ProfileView(oak);
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    void OakProfile()
    {
        //oak = new Monster(); // 생성자 // 기본 생성자 일때 주석 2줄 사용
        //oak.name = "거친 오크";
        oak = new Monster("거친오크");
     // 매개변수 생성자 사용 했을때 이렇게 사용가능 new Monster(string name);
        
        oak.sort = Monster.SORT.DEMON;
        oak.hp = 1000;
        oak.address = "늪";
        oak.isBoss = false;
        oak.isFirstAttack = false;
        oak.isCall = false;
    }

    public void ProfileView(Monster mon) 
    {
        Debug.Log("몬스터 이름: " + mon.name);
        Debug.Log("몬스터 분류: " + mon.sort);
        Debug.Log("몬스터 체력: " + mon.hp);
        Debug.Log("몬스터 서식지: " + mon.address);
        Debug.Log("몬스터 보스유무: " + mon.isBoss);
        Debug.Log("몬스터 선공형 유무: " + mon.isFirstAttack);
        Debug.Log("몬스터 호출형 유무: " + mon.isCall);
    }

}**

```

세 번째 Monster Manager

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MonsterManager : MonoBehaviour
{

    public Monster monster;

    // Start is called before the first frame update
    void Start()
    {
        monster = this.GetComponent<Monster>(); 
        // 컴포넌트 자동 연결 해주는 함수
	          GetComponent<객체 이름?>();
        // 유니티에서 드래그 없이 자동으로 연결해주는 코드 매우 중요함.
        //Debug.Log(monster.name);   
        GameManager.instance.ProfileView(monster);
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}**

```

**네 번째 바바리안 보스** 

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class BabaBoss : <u>Monster</u> // 클래스 상속 Monster로 받음 
{
    // 객체지향 상속
    // <u>기존 몬스터의 기능을 그대로 이어받고</u>
    // <u>새로운 기능을 추가할수 있음</u>

    int powerfulLv = 0; // 강력기 레벨
    public int maxHp;
    public int currentHp;

    // Start is called before the first frame update
    void Start()
    {
        GameManager.instance.ProfileView(this.GetComponent<Monster>());
        maxHp = base.hp; //원래 기능 hp사용 base
        currentHp = base.hp;
    }

    // Update is called once per frame
    void Update()
    {
        ChangePowerLevel();
    }

    public void ChangePowerLevel()
    {
        if (currentHp <= maxHp * 0.8)
        {
            powerfulLv = 1;
            BossEffectColor();
            Debug.Log("강력기 발동 레벨 : " + powerfulLv);
        }

    }

    public virtual void BossEffectColor()
    {
        Debug.Log("보스 이펙트 출력 빨간색");
    }
}**

```

**다섯 번째 바바보스 파란색**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class BabaBossBlue : BabaBoss
{

    // Start is called before the first frame update
    void Start()
    {
        base.maxHp = base.hp; 
        //base 생략가능한데 다른 사람이나 내가 보기쉽게 해주는게 좋다. 
        base.currentHp = base.hp;  
    }

    // Update is called once per frame
    void Update()
    {
        base.ChangePowerLevel();
    }

    public override void BossEffectColor()
    {
        //base.BossEffectColor();

        Debug.Log("이펙트 파란색");
    }
}**

```

<u>**base.함수() : 상속 받은 것을 사용할때 base. 쓰는것이 분별력에도 좋고, 아버지, 할아버지 도 같이 base씀.  단, 사용하는 함수 public 해야함.**</u>

![Untitled](Untitled%206.png)

**Mesh Renderer : 색상 입히는거** 

**Box Collider :** 

![Untitled](Untitled%207.png)

![Untitled](Untitled%208.png)

**기본 구형태에 색상? 을 입힐때 쓰는 법**

**Shader에 Legacy Shaders를 클릭해서 Diffuse 클릭 하고 에셋 스토어에 다운 받은 원하는 디자인을 끌어다가 Select에 놓으면 디자인이 입혀짐.**

![Untitled](Untitled%209.png)

**Input Manager : 이동키 조작 설정 가능**

**C#에 접근하기 위해서 Input.Axes.쓰려고하는 함수명(오타 나면 안됨.)을 써서 구현한다.**

**나중에 가면 쓰는 함수명을 설정해서 짧게 해서 할수도 있음(이건 숙련자가 쓰는게 좋음)**

**전체 코드(이동, 좌우 돌리기 ,총쏘기)** 

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    [SerializeField]
    float speed = 10; // 초속 10M
  
    [SerializeField]
    public float rotSpeed = 120; // 초속 120도
    

    // Start is called before the first frame update
    void Start()
    {
       
    }

    // Update is called once per frame
    void Update()
    {
        MoveStart();
    }

    void MoveStart()
    {
        float move = speed * Time.deltaTime;
         // Time .deltaTime 실시간 타임 값 이거 안쓰면 굉장히 빠르게 움직임
        float rot = rotSpeed * Time.deltaTime;
        float keyS = Input.GetAxis("Horizontal");
        float keyF = Input.GetAxis("Vertical"); 
        // 프로젝트 세팅에 있는 이름 그대로 써서 키입력 가능하게 함. // Vertical은   디폴트로 -로 되어 있음
        
        //유니티 이동함수 Translate
        transform.Translate(Vector3.forward * move * keyF); // 키보드 입력없이 혼자서 이동함.

        //유니티 회전함수 Rotate
        // 유니티 회전은 회전축을 기준으로 설정된다고 생각.                            tip : Vector3.right 기준 왼팔 뻗고 오른손  붙인다음에 돌리기?
        // 회전축 공부는 해야할듯
        transform.Rotate(Vector3.up * rot * keyS);
    }
}**

```

**이동, 좌우** 

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    [SerializeField]
    float speed = 10; // 초속 10M
  
    [SerializeField]
    public float rotSpeed = 120; // 초속 120도
    

    // Start is called before the first frame update
    void Start()
    {
       
    }

    // Update is called once per frame
    void Update()
    {
        MoveStart();
    }

    void MoveStart()
    {
        float move = speed * Time.deltaTime;
         // Time .deltaTime 실시간 타임 값 이거 안쓰면 굉장히 빠르게 움직임
        float rot = rotSpeed * Time.deltaTime;
        float keyS = Input.GetAxis("Horizontal");
        float keyF = Input.GetAxis("Vertical"); 
        // 프로젝트 세팅에 있는 이름 그대로 써서 키입력 가능하게 함. // Vertical은   디폴트로 -로 되어 있음
        
        //유니티 이동함수 Translate
        transform.Translate(Vector3.forward * move * keyF); // 키보드 입력없이 혼자서 이동함.

        //유니티 회전함수 Rotate
        // 유니티 회전은 회전축을 기준으로 설정된다고 생각.                            tip : Vector3.right 기준 왼팔 뻗고 오른손  붙인다음에 돌리기?
        // 회전축 공부는 해야할듯
        transform.Rotate(Vector3.up * rot * keyS);
    }
}**

```

![Untitled](Untitled%2010.png)

**1인칭 게임 이나 3인칭 <u>게임 할때 메인 카메라는 한개 무조건 있어야</u> 하고** 

**캐릭터의 시점을 메인 카메라가 따라가기 위해서는 메인 카메라를 끌어서 Player에 종속 받게 한다.** 

**총 쏘는 방법**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    
    public Transform bullet; //총알 프리팹 받을거임
    public Transform spPoint; // 총알 생성 위치

    bool isFire = false; //코루틴 제어 플래그

    // Start is called before the first frame update
    void Start()
    {
       
    }

    // Update is called once per frame
    void Update()
    {
        FireStart();
    }
    
    void FireStart()
    {
        // GetButton 버튼을 누르고 있을때
        // GetButtonDown 버튼을 눌렀을때
        // GetButtonUp 버튼을 떼었을때

        //if (Input.GetButton("Fire1")) //Fire1 마우스 왼쪽 버튼, 왼 ctrl 
        //if (Input.GetButton("Fire2")) //Fire2 마우스 오른쪽 버튼, 왼 alt
        //if (Input.GetButton("Fire3")) //Fire3 마우스 휠 버튼, 왼 shift

        if (Input.GetButton("Fire1")) 
        {
            //Debug.Log("동작");
            
						// 내가 있는 곳에 생성 	
            //Instantiate(bullet, this.transform.position, this.transform.rotation);          
            

//Transform tr = Instantiate(bullet, this.transform.position, this.transform.rotation);

// Instantiate 유니티 오브젝트 생성 함수 형식이 같아야 쓸수 있음 현재 bullet은 Transform형 임. 그래서 .gameObject로 해줌
            
            // StartCoroutine 코루틴 호출 시작 함수 첫 번째 방식 
            <u>StartCoroutine(OnFire());</u>

// StartCoroutine 코루틴 호출 시작 함수 두 번째 방식 -> 언제 쓰나? : 정지 코루틴 함수 사용할때
            //<u>StartCoroutine("OnFire");</u>

// 정지 코루틴 함수, 문자열로 시작함수와 정지함수를 둘다 문자열로 받아야 동작됨(코루틴 호출 두번째 방법)
//<u>StopCoroutine("OnFire");</u> 

            // <u>StopAllCoroutines();</u> // 모든 코루틴 함수 정지

        }
    
    }

    // IEnumerator - 코루틴 함수 정의
    IEnumerator OnFire()
    {
        if (!isFire) // == (isFire == false)
        {
            isFire = true;

            Transform tr = Instantiate(bullet, spPoint.position, spPoint.rotation);

            tr.GetComponent<Rigidbody>().velocity = this.spPoint.forward * 50f;

            yield return new WaitForSeconds(0.1f); 
            //yield return new 여기 까진 외워야함 상황에 따라 
            WaitForSeconds와 관련된 것들을 써야함.
            isFire = false;

        }
    }
}**

```

**총 쏠때 자기 자신 안맞게 하는법?**

**1. z축을 기점으로 방향 설정**

**2. gameobject(Create Empty)를 생성해서 2D로 바꿔서 오브젝트와 엠티를 z축으로 수평에 맞게 맞춘다.**

![Untitled](Untitled%2011.png)

 

![Untitled](Untitled%2012.png)

**3. 위치를 top으로 맞춰서 알맞게 설정한다.**

![Untitled](Untitled%2013.png)

**총알과 sp Point를 스크립트에 넣는다.**

**적 생성 및 충돌 함수**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Enemy : MonoBehaviour
{
    //[SerializeField] // 첫 번째 방법 : 직렬화 -> 외부 접근 불가능
    //Transform player;

    //public Transform player; // 두 번째 방법 : 직렬화2(public) -> 외부 접근 가능

    Transform player; // 세 번째 방법 Tranform

    
    public Transform bullet; // 총알 프리팹

    public Transform spPoint;

    float delayTime = 0;

    // Start is called before the first frame update
    void Start()
    {
        // player = GameObject.Find("Player").transform; // 오브젝트 참조 방식 1
        //player = GameObject.FindWithTag("Player").transform; // 오브젝트 참조 방식 2 -> 태그가 입혀져야 함.
        player = GameObject.FindGameObjectWithTag("Player").transform; // 오브젝트 참조 방식3

        //player = GameObject.FindGameObjectsWithTag("Player").transform; // 오브젝트 참조 방식(배열용)

        //Debug.Log(player.name);    
    }

    // Update is called once per frame
    void Update()
    {
        transform.LookAt(player); // 해당() 오브젝트를 주시(회전)
        
        OnFire();
    
        delayTime -= Time.deltaTime;
    }

    void OnFire() // 비 권장하는 기법
    {
        if(delayTime <=0)
        {
            delayTime = 1;

            Transform tr = Instantiate(bullet, spPoint.position, spPoint.rotation);
            tr.GetComponent<Rigidbody>().velocity = spPoint.forward * 30f;
        }
    }
}**

```

**총알**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Bullet : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Destroy(gameObject, 1.5f); // 총알 1.5초 후에 사라짐, 근데 이 방법은 안씀 
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    // 유니티 충돌 이벤트 함수
    // 충돌체에 isTrigger가 체크되었을때 발동
    // Rigidbody가 한쪽에 필수적으로 설치 되어야 함.
    // 매개변수 other : 상대 충돌체 

    

    private void OnTriggerEnter(Collider other) // 충돌 시작 발생
    {
        //Debug.Log("OnTriggerEnter");

        if (other.tag == "Enemy")
        {
            //Debug.Log("OnTriggerEnter");
            Destroy(other.gameObject); // 총알 충돌(맞았을때) 적은 파괴된다?
        }
    }

    private void OnTriggerStay(Collider other) // 충돌중일때 발생
    {
        Debug.Log("OnTriggerStay");
    }

    private void OnTriggerExit(Collider other) //충돌체 빠져나갔을때 발생
    {
        Debug.Log("OnTriggerExit");
    }

    // 유니티 충돌 이벤트 함수 2
    private void OnCollisionEnter(Collision collision)
    {
        Debug.Log("OnCollisionEnter");
    }

    private void OnCollisionExit(Collision collision)
    {
        Debug.Log("OnCollisionExit");
    }

}**

```

**사거리 설정방법**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Enemy : MonoBehaviour
{
    //[SerializeField] // 첫 번째 방법 : 직렬화 -> 외부 접근 불가능
    //Transform player;

    //public Transform player; // 두 번째 방법 : 직렬화2(public) -> 외부 접근 가능

    Transform player; // 세 번째 방법 Tranform

    
    public Transform bullet; // 총알 프리팹

    public Transform spPoint;

    float delayTime = 0;

    // Start is called before the first frame update
    void Start()
    {
        // player = GameObject.Find("Player").transform; // 오브젝트 참조 방식 1
        //player = GameObject.FindWithTag("Player").transform; // 오브젝트 참조 방식 2 -> 태그가 입혀져야 함.
        player = GameObject.FindGameObjectWithTag("Player").transform; // 오브젝트 참조 방식3

        //player = GameObject.FindGameObjectsWithTag("Player").transform; // 오브젝트 참조 방식(배열용)

        //Debug.Log(player.name);    
    }

    // Update is called once per frame
    void Update()
    {
        transform.LookAt(player); // 해당() 오브젝트를 주시(회전)
        
        SearchPlayer();

        //OnFire();
    
        delayTime -= Time.deltaTime;

        

    }

    void OnFire() // 비 권장하는 기법
    {
        if(delayTime <=0)
        {
            delayTime = 1;

            Transform tr = Instantiate(bullet, spPoint.position, spPoint.rotation);
            tr.GetComponent<Rigidbody>().velocity = spPoint.forward * 30f;
        }
    }

    void SearchPlayer() // 이 함수 아래 부분 외워야함.
    {
        //프로그래밍에 영향을 주지 못함(확인용) 아래 코드로 범위를 알수 있음 
        Debug.DrawRay(spPoint.position, spPoint.forward * 15, Color.red);// DrawRay(시작값, 범위?, 색깔);

        RaycastHit hit; //광선 충돌체

        if (Physics.Raycast(spPoint.position, spPoint.forward, out hit, 15)) // 가장 많이 쓰는 기법 
        {
            if ((hit.transform.tag == "Player"))
            {
                //Debug.Log("광선 충돌 발생");
                OnFire();
            }

        }

    }

}**

```

