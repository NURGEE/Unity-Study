# 2주차(조건문, 에셋 스토어 사용법,  )

```
     **관계연산자 종류
     < : 좌변보다 우변이 클 때
     > : 좌변보다 우변이 작을 때
     == : 좌변 우변이 같을 때
     <= : 좌변보다 우변이 크거나 같을 때 (=연산자는 우측에 있어야 함)
     >= : 좌변보다 우변이 작거나 같을 때 
     != : 좌변 우변 같지 않을 때 
     ! : 원래 결과 값의 반대

 나머지 연산자 : 배수 확인용으로 많이 활용
     %
        
 프리팹 : Instantiate(); // 유니티 생성 함수
 
 삼항 연산자 (if문과 비슷함) -> ? true : false
     ?앞의 내용에대해  true면 이쪽 부분을 출력 : false면 이쪽 부분을 출력
 
 논리 연산자
 or 논리합 : ||(버틸컬바) > 좌항과 우항중 하나만 참이면 수행
 and 논리곱 : &&(에퍼센트?) > 좌항 우항 모두 참이면 수행

실습 사항
  랜덤 값을 사용하여 평균점수가 90점 이상이면 A
  80점이면 B, 70점이면 C, 60점이면 D, 50점 이하면 F 출력
  평균점수의 결과값이 +5이상의 점수가 있다면 +추가
  if(avg >= 65 && (avg % 10 >= 5 || avg == 100)) grade = grade +"+";**
```

```
**// 배열 학습
string stud_1 = "도깨비";  //일반 변수, 초기화
string stud_2 = "홍길동";
string stud_3 = "슈퍼맨";
string stud_4 = "몬스터";

string[] myArray = new string[4]; // 배열 선언과 크기를 지정
//string[] familyMember = new string[] { "도깨비", "홍길동", "슈퍼맨", "몬스터" };
string[] familyMember ={ "도깨비", "홍길동", "슈퍼맨", "몬스터" };

//리스트 학습 -> 게임 인벤토리 만들때 사용?
 List<string> familyMembers = new List<string>(); // 리스트 선언법
 
 //어레이리스트 학습
public ArrayList inven = new ArrayList(); // 어레이리스트 선언, 사용법은 리스트와 같음

public Text danHead;
public Text[] danBoard;
public int dan = 2;
List<int> gugudan = new List<int>();

 
 myArray[0] = "도깨비";
 myArray[1] = "홍길동";
 myArray[2] = "슈퍼맨";
 myArray[3] = "몬스터";

 Debug.Log(myArray[0]);
 Debug.Log(myArray.Length); // 길이 검사

 //리스트 사용법
 familyMembers.Add("도깨비"); //멤버 추가
 familyMembers.Add("홍길동");
 familyMembers.Add("슈퍼맨");
 familyMembers.Add("몬스터");
 Debug.Log(familyMembers[3]);

 //familyMembers.Remove("몬스터"); //지정한 멤버 삭제
 //Debug.Log(familyMembers[3]);
 //familyMembers.Clear(); //전체 삭제
 //Debug.Log(familyMembers[0]);

 Debug.Log(familyMembers.Contains("백성재")); // Contains : .앞에 변수의 ()안에 있는 내용이 있는지 확인하는 함수 맞으면 true, 아니면 false
 familyMembers.Insert(4, "백성재"); // Insert : .앞에 변수의 4번째 공간에 백성재 추가하는 함수
 Debug.Log(familyMembers.Contains("백성재"));

 Debug.Log(familyMembers.Count); // Count : 리스트 길이 검사
 
inven.Add(10);
inven.Add(10.6f);
inven.Add("백성재");
inven.Add(GameObject.Find("Player"));
Debug.Log(inven[3]);

gugudan.Add(dan);
//danHead.text = gugudan[0].ToString();
danHead.text = gugudan[0] + "단";

public void PlusDan()
{
    gugudan.Add(dan);
    int tmpNum = gugudan[0];

    gugudan.Remove(0);
    gugudan.Insert(0, tmpNum+1);
    danHead.text = gugudan[0] + "단";

    //danHead.text = (gugudan[0] += 1) + "단";
}
public void MinusDan()
{
    danHead.text = (gugudan[0] -= 1) + "단";
}

public void GuguDan()
{
    danBoard[0].text = gugudan[0] + " * 1 = " + gugudan[0] * 1;
    danBoard[1].text = gugudan[0] + " * 2 = " + gugudan[0] * 2;
    danBoard[2].text = gugudan[0] + " * 3 = " + gugudan[0] * 3;
    danBoard[3].text = gugudan[0] + " * 4 = " + gugudan[0] * 4;
    danBoard[4].text = gugudan[0] + " * 5 = " + gugudan[0] * 5;
    danBoard[5].text = gugudan[0] + " * 6 = " + gugudan[0] * 6;
    danBoard[6].text = gugudan[0] + " * 7 = " + gugudan[0] * 7;
    danBoard[7].text = gugudan[0] + " * 8 = " + gugudan[0] * 8;
    danBoard[8].text = gugudan[0] + " * 9 = " + gugudan[0] * 9;
}**
```

```
    
**1. while문 
 
 void UserWhile()
 {
     int num = 0; //반복 제어 변수

     while(num <5) 
     {
         Debug.Log("일요일 유니티!");
         
         num++; // 주의 해야할점 : 증감 연산자를 빼먹으면 무한루프에 걸려서 유니티가 아예 멈추기 때문에 실행하기 전에 유니티 저장하고 실행하던가 while문 무한루프 안걸리게 해주자. 
     }

     
 }**

```

```
**2. do while문

 void UserDoWhile()
 {
	  int num = 0;
		 do
		 {
       Debug.Log("do-while 일요일 유니티!");
	     num++;      
  
     } while ( num <5);
 }**
```

```
**3. for문

 void UserFor()
 {
     for(int num = 0; num < 5; num++)
     {
         Debug.Log("for 일요일 유니티!");

     }
 }**
```

```
**3. foreach문 : 유니티에선 권장하진 않음 이유는 복제를 하기 때문 

 foreach(<u>string member in familyMember</u>) -> member에 familyMember를 복제.
{
    Debug.Log("foreach: " + member);
}**
```

