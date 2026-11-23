# 유니티2 다른 강사님(8월 애니메이션, 캐릭터, 맵 동작 구현 8/25)

```
    **이 캐릭터 애니메이션 총괄하는 컨트롤러 생성하는 방법
    캐릭터를 먼저 끌어다 놓고 생성되면 프로젝트에 우클릭 create -> animator controller 클릭 그러고 하이라키에 캐릭터 누르고 이 생성된 컨트롤러를 끌어다 놓으면 컨트롤러가 들어감 
	  애니메이션 컨트롤러를 클릭하면 애니메이터가 나오는데 거기서 ide를 우클릭해 초기 지정해놓고 다음으로 선 추가해서 러닝과 상호 연결 시키고 ide -> running 선에는 
	  파라미터에 조건 추가한다. 그리고 인스펙터에 컨디션 추가 해서 running 으로 변경
	  반대로 running -> ide 로 갈떄또 위에 처럼 해주기 이렇게하면 아래 enum 안해도 될거?임.
     
    스크립트(PlayerCtrLcs) 생성해서 캐릭터에 넣고 
    Start에는 
    NavMeshAgent의 컴포넌트를 agent에 저장한다.
    Animator의 컴포넌트를 anim에 저장한다.
    Rigidbody의 컴포넌트를 rigidBD에 저장한다.
    
    
    그리고 
    enum STATE{
	    _IDLE_,
	    _RUN_,
	    _ATTACK_,
	    _DEAD_,
    };
    
    STATE st = STATE._IDLE_;
    코드 추가
    
    그리고
    
    Update에는 
    
    switch(st)
    {
	    case STATE._IDLE_:
	    {
		     if(Input.GetMouseButtonDown(0))
			    {
				    agent.isStopped = false; // 움직일 수 있게
				    Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
				    RaycastHit hit;
	    
				    if(Physics.Raycast(ray, out hit))
				    {
					    agent.SetDestination(hit.point);
				    }
    
			    }
	    }
	    break;
	    case STATE._RUN_:
	    {
	    
	    }
	    break;
	    case STATE._ATTACK_:
	    {
		   
	    }
	    break;
	    case STATE._DEAD_:
	    {
	    
	    }
	    break;
    }
  
    파라미터 추가 했으니까 스크립트에 추가 코드 작성하기
    Raycast 조건문 안에 anim.SetTrigger("run"); 추가
    그리고 멈출때는 RUN안에 코드 추가
    
    if(!agent.pathPending)
    {
	    if(agent.remainingDistance <= agnet.stoppingDistance)
	    {
		    if(!agent.hasPath || agent.velocity.sqrMagnitude == 0f)
		    {
		    
			    anie.SetTrigger("idle");
					st = STATE._IDLE_; // 도착 했으니 STATE 바꾸기 
		
		    }
	    }
    }
    
    정지해서 공격 모션 추가하기?
    스크립트 enum문단 아래에 따로 
    bool isDuringAttack = false; // 공격 중인지 판단하는 변수
    
    public void setAttackEnd()
    {
	    isDuringAttack = true;
    }
    
    그리고 먼저 Update 안에 공격했을때 공격중인 변수를 true로 바꾼다.
    if(Input.GetMouseButtonDown(1))
    {
	    isDuringAttack = true;
    } 코드 추가
     다음 switch문 IDLE 부분 안에
    if(isDuringAttack)
    {
	    agent.ResetPath();
	    agent.velocity = Vector3.zero;
	    agent.isStopped = true;
	    
		  anim.SetTrigger("attack");
	    st = STATE._ATTACK_;
    }
    코드 추가하고 애니메이터에 가서 공격 모션과 IDLE을 연결한다 
    그리고 파라미터에 트리거 하나 생성하고 변수명을 attack으로 설정하고 인스펙터 컨디션 추가하고 어택으로 변경하면 됨. 이 설정은 idle -> 공격모션으로 가는 선을 설정하면됨.
이제 그 반대 선로는 exit Time 체크 해야됨 
		
		그리고 switch문 attack 안에 
		if(isDuringAttack == false)
		{
			st = STATE._IDLE_;

		}
코드 추가
			
		공격이 끝나는 시점을 감지? 하는 방법
		프로젝트에 공격모션을 클릭하고 인스펙터에 이벤트를 클릭하고 function에는 setAttackEnd로 이름을 바꾸고 해당하는 스크립트를 지정한다.  
	
	하이라키에 캐릭터에 뼈대 구조들을 펼치고 검 클릭하고 오른 마우스 클릭해서 create empty 충돌 체크를 한다. 생성한 엠티에 Sphere collider 컴포넌트 추가. 
	엠티 위치는 검 중앙 부분으로 옮긴다. 
	스크립트 생성해서 empty 에 끌어다 놓는다.
	
	그 안에 
	private void OnTriggerEnter(Collider other)
	{
			if(transform.root.GetComponent<PlayerCtrl().isAttacking())
			{
			
			}
	
	} 	코드 추가
    다시 캐릭터 스크립트로 돌아가서 
    public bool isAttacking()
    {
		    return isDuringAttack;
    
    } 코드 추가 
    
    
    캐릭터에 카메라를 따라가게 하는법(3인칭?)
    ->하이라키에 메인 카메라를 드래그 해서 캐릭터에 넣는다
     
    벽이런거 레이어를 구분한 다음 edit preference클릭 한 다음 지정해주기?**
    
     
```

![image.png](image%203.png)

