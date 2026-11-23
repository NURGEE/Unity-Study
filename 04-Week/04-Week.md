# 4주차(물리(중력 적용), 내비게이션?(AI))

<u>**Add Component = Rigidbody(중력 적용 컴포넌트)**</u>

![Untitled](Untitled%2014.png)

**콘솔에 실행되는 순서가 컴파일 순서임** 

![Untitled](Untitled%2015.png)

**Unity할때 이 함수들을 주로 씀. 필수로 알아둘 것.**

![Untitled](Untitled%2016.png)

**AI를 입힐때 Navigation누르고 Bake에서 Bake를 눌러야 함. + static을 체크 해야됨.**

**플레이어 이동**

```
    **using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    [SerializeField]
    int speed = 10;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        float move = speed * Time.deltaTime;
        float KeyF = Input.GetAxis("Vertical");
        float KeyS = Input.GetAxis("Horizontal");

        transform.Translate(new Vector3 (KeyS, 0, KeyF) * move);

    }
}**

```

**Enemy의 AI 관련 코딩**

```
    **using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public class Enemy : MonoBehaviour
{
    [HideInInspector] // 컴포넌트 참조를 숨길수 있는 속성 기능
    public NavMeshAgent agent;

    public Transform player;

    Vector3 oriPos;

    // Start is called before the first frame update
    void Start()
    {
       agent = this.GetComponent<NavMeshAgent>(); // 참조 1
       oriPos = transform.position;
        
       
    }

    // Update is called once per frame
    void Update()
    {
        //agent.destination = player.position;

        float dist = Vector3.Distance(this.transform.position, player.position);

        //Debug.Log(dist);

         //추적과 복귀
         
        if(dist <15) //  추적
        {
            agent.stoppingDistance = 3;
            agent.destination = player.position;
        }
        else // 북귀
        {
            agent.stoppingDistance = 0;
            agent.destination = oriPos;
        }
    }
}**

```

 **비행기 슈팅게임?**

![Untitled](Untitled%2017.png)

**하늘** 

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Sky : MonoBehaviour
{
    float scrollSpeed = 0.3f;
    float offset;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        offset += scrollSpeed * Time.deltaTime;
        transform.GetComponent<Renderer>().material.mainTextureOffset = new Vector2(0, -offset);
    }
}**

```

**비행기**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Wing : MonoBehaviour
{
    int speed = 40;

    public Transform missile; // 미사일 프리팹

    public Transform rPos;
    public Transform lPos;

    bool isFire = true; // 코루틴 제어 플래그

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        OnMove();

        Onfire();
    }

    // 이동 함수 정의
    void OnMove()
    {
        float move = speed * Time.deltaTime;
        float keyF = Input.GetAxis("Vertical"); // 상하
        float keyS = Input.GetAxis("Horizontal"); // 좌우

        //transform.Translate(Vector3.forward * move * keyF);
        //transform.Translate(Vector3.right * move * keyS);
        //transform.Translate(new Vector3(keyS, 0, keyF) * move);

        //Debug.Log(Screen.width); 
        //Debug.Log(Screen.height); 

        //메인 카메라 접근
        Vector3 pos = Camera.main.WorldToScreenPoint(transform.position);

        //Debug.Log(pos);

        //if (pos.x < 0) Debug.Log("posX 왼");
        //if (pos.x > Screen.width) Debug.Log("posX 오");

        float fw = Screen.width * 0.08f;
        float fh = Screen.height * 0.08f;

        //Debug.Log(fw);
        
        //if(pos.x < Screen.width - fw) //화면 상 오른쪽까지
        //{
        //    transform.Translate(Vector3.right * move);
        //}

        if (keyS < 0 && pos.x > fw || (keyS > 0 && pos.x < Screen.width -fw )) //게임 화면상 왼쪽부터 오른쪽 까지 그외 밖에는 벗어나지 않음.(화면 해상도가 변해도 벗어나지 않음)
        {
            transform.Translate(Vector3.right * move * keyS);
        }
        if (keyF < 0 && pos.y > fh || (keyF > 0 && pos.y < Screen.height -fh )) // 게임 화면상 위에서 아래까지 벗어나지 않음
        {
            transform.Translate(Vector3.forward * move * keyF);
        }
        
        
    }

    void Onfire()
    {
        if(Input.GetButton("Fire1") && isFire)
        {
            StartCoroutine(MakeMissile());
        }
    }

    IEnumerator MakeMissile()
    {
        isFire = false;

        
        // 2방식
        //Instantiate(missile, transform.position, transform.rotation).GetComponent<Rigidbody>().velocity = Vector3.forward * 50;

        // 1방식
        Transform tr_1 = Instantiate(missile, rPos.position, rPos.rotation);
        Transform tr_2 = Instantiate(missile, lPos.position, lPos.rotation);

        tr_1.GetComponent<Rigidbody>().velocity = Vector3.forward * 50f;
        tr_2.GetComponent<Rigidbody>().velocity = Vector3.forward * 50f;

        yield return new WaitForSeconds(0.1f);

        isFire = true;
    }
}**

```

**미사일, 이펙트 구현**

![Untitled](Untitled%2018.png)

![Untitled](Untitled%2019.png)

**JetFire = 이펙트의 파티클 시스템 클릭후 오른쪽 Inspector 에서 파티클 시스템, 에미션, 섀이프, 컬러 오버 라이프 타임, 사이즈 오버 라이프 타임 렌더링 건들기.** 

**파티클 시스템**

![Untitled](Untitled%2020.png)

**에미션, 섀이프 : ? , 이펙트 분출의 모양**

![Untitled](Untitled%2021.png)

**Color over Lifetime, Size over Lifetime : 이펙트의 색상, 이펙트의 모형?** 

![Untitled](Untitled%2022.png)

**미사일**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Missile : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
       if(transform.position.z > 35)
        {
            Destroy(this.gameObject);
        }
    }

    //충돌 처리 함수
    private void OnTriggerEnter(Collider other)
    {
        //if(other.tag == "AST") 둘중에 하나 사용
        if(other.CompareTag("AST"))
        {
            other.SendMessage("Hit", SendMessageOptions.DontRequireReceiver); // 문자열만 가능
            //other.GetComponent<Ast>().Hit();
            //other.GetComponent<Test>().Hit();
            
            Destroy(gameObject); // 현재 오브젝트 제거
            
        }
    }

   

}**

```

**울퉁불퉁한 효과를 내려고 하는 기법 : 노말맵(NMR)**

![Untitled](Untitled%2023.png)

**운석**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Ast : MonoBehaviour
{

    Transform tr;
    int speed;
    int hp;

    private void Awake()
    {
        tr = transform; // 자기 자신 참조캐싱 : 속도 엄청 빠름
    }
    // Start is called before the first frame update
    void Start()
    {
        tr.localScale = new Vector3(Random.Range(3, 6), Random.Range(3, 6), Random.Range(3, 6));
        speed = Random.Range(10, 20);
        hp = Random.Range(0, 5);
        tr.position = new Vector3(Random.Range(-28, 28), 0, Random.Range(30, 70));
    }

    // Update is called once per frame
    void Update()
    {
        float move = speed * Time.deltaTime;

        tr.Translate(Vector3.back * move);

        Destroy(tr.gameObject, 6.0f);
    }

    //미사일과 충돌시 적용
    public void Hit()
    {
        //Debug.Log("운석 함수!!");
        if(hp > 0)
        {
            hp--;
            //Debug.Log("운석체력: " + hp);

            Transform obj = Instantiate(GameManager.instance.expHit, tr.position, Quaternion.identity);
            Destroy(obj.gameObject, 2.0f);
        }
        else
        {
            Transform obj = Instantiate(GameManager.instance.expDestroy, tr.position, Quaternion.identity);
            Destroy(obj.gameObject, 2.0f);

            Destroy(tr.gameObject); // 현재 오브젝트 소거(운석 제거)
        }

    }

}**

```

**게임 매니저(운석) → 프리팹 운석을 가져와서 씀(피격 효과 포함)**

```
**using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameManager : MonoBehaviour
{

    public Transform ast; // 운석 프리팹

    public Transform expHit; // Hit할때  
    public Transform expDestroy; // 파괴 될때

    bool isSpawn = false; //코루틴 제어 플래그

    public static GameManager instance; // 싱글톤(턴) 기법

    private void Awake()
    {
        instance = this;
    }

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update() // StartCoroutine(MakeAst()); , MakeAsteroid(); 둘중에 하나 쓰면 됨.
    {
       //StartCoroutine(MakeAst()); 

        MakeAsteroid();
    }

    //코루틴 활용, 운석 생성 
    IEnumerator MakeAst()
    {
        if(isSpawn)
        {
            isSpawn = true;

            Instantiate(ast, ast.transform.position, ast.transform.rotation);
            yield return new WaitForSeconds(Random.Range(0, 3));

            isSpawn = false;
        }
    }

    void MakeAsteroid()
    {
        int num = Random.Range(0, 1000);

        if(Random.Range(0,1000) > 980) // 확률로 인스턴스 생성
        {
            Debug.Log(num);

            Instantiate(ast);
        }
    }
}**

```

**컴파일 결과**

![Untitled](Untitled%2024.png)

