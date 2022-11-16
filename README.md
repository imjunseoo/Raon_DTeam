![image](https://user-images.githubusercontent.com/101697176/202194053-56537384-f8ab-45ae-8d43-8c49cc5cbabe.png)



# Raon_DTeam
> __20225238 임준서__  
> __20203830 이은비__  
# 라온 D_Team SW전시회 진행 보고서
> __개발중인 게임의 가제 : 여우의 모험__ | *플레어블 캐릭터의 모델링이 여우이기 때문에 여우의 모험으로 정했습니다.*     
> __프로젝트 컨셉 :__ *여러가지 미니게임들을 종합적으로 모아놓아서 최종적으론 점수를 기록하며 즐길 수 있는 라이트한 게임입니다.*     
> __디자인 : Unity AssetStore__ | *에셋스토어를 이용하여 최대한 무료에셋을 이용하여 부족한 디자인적인 측면을 채우고자 하였습니다.*    


## 현재 구상중인 게임모드 3가지
* [x] __장애물 피해서 빠르게 달리기__ / 임준서  
* [ ] __적을 피하며 미로 도망치기__/ 미정  
* [x] __토끼 or 닭 사냥하기__ / 이은비  
> *미니게임은 더 추가할 예정이며 현재 각자 1개씩 게임을 맡아 협업중입니다.*

## 현재 게임 개발 상황(임준서)
> 조작법은 WASD를 기본으로 LShift 달리기, Space바 점프로 구성되어있습니다.
### Player Move 코드
```c#
void Movement()
    {
        float x = Input.GetAxisRaw("Horizontal");
        float z = Input.GetAxisRaw("Vertical");        
        
        moveDirection = Vector3.Lerp(moveDirection, new Vector3(x, 0, z), lerpSpeed * Time.deltaTime);

        characterController.Move(moveDirection * movespeed * Time.deltaTime);
        transform.position = new Vector3(transform.position.x, 0f, transform.position.z);
        if (x != 0 || z != 0)
        {
            anim.SetBool("isRun", true);
            transform.rotation = Quaternion.LookRotation(moveDirection.normalized);
        }
        else
        {
            anim.SetBool("isRun", false);
        }
    }
```
> *Character Controller를 이용하여 움직임을 구현했습니다. 또한 Character Controller를 이용하면 중력을 구현해야 했기에 중력또한 구현하였습니다.*
```c# 
 void Update()
    {        
        Movement();
        Sprint();
        if (characterController.isGrounded == false)
        {
            anim.SetBool("isJump", true);
            moveDirection.y += gravity * Time.deltaTime;
        }
        else if (characterController.isGrounded == true) 
        {
            anim.SetBool("isJump", false);
        }
        if (Input.GetKeyDown(KeyCode.Space)) 
        {
            Jump();
        }
        
    }
```
> *Character Controller의 isGrounded를 이용하여 점프중 중복하여 점프가 되지 않도록 하였고 중력을 구현하기 위하여isGrounded와 if문을 이용하여 플레이어의 moveDirection에 대입연산자를 이용하여 실제중력과 비슷한 9.81의 값을 계속하여 감소시키게 하였습니다.*

### 장애물) 플레이어를 추적하는 오브젝트 코드

```C#
public class Stalker : MonoBehaviour
{
    private float rotspeed = 100f;
    Rigidbody rigid;        
    
    private void Start()
    {
        rigid = GetComponent<Rigidbody>();
    }



    private void FixedUpdate()
    {
        transform.Rotate(new Vector3(rotspeed * Time.deltaTime, 0, 0));
        rigid.AddForce(Vector3.forward, ForceMode.Impulse);
    }


}
```
> *Rigidbody를 이용한 AddForce로 통나무가 플레이어를 향하여 굴러가며 플레이어가 긴박함을 느낄 수 있게 해주는 장치를 구현하였습니다.* <br>

### 개발해야하는 것들
* [ ] ___회전 장애물 구현___
* [ ] ___OnCollisionEnter를 이용한 collision의 충돌 시 플레이어에게 디버프를 부여하는 장애물아이템 만들기___
* [ ] ___맵 디자인___
* [x] ___플레이어의 움직임, 달리기, 점프, HP, 스태미나 구현___
* [ ] ___점수 시스템과 플레이어 UI제작___
* [x] ___플레이어를 추적하는 통나무 장애물 구현___

## 현재 게임 개발 상황(이은비)
> 여우(플레이어) 움직임 구현: 캐릭터가 움직일때 카메라도 같이 움직이도록 구현함

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Foxmove : MonoBehaviour
{
    public float Speed = 5.0f;

    public float rotateSpeed = 2.0f;       // 회전 속도

    public float jumpForce = 1.0f;

    Rigidbody body;

    public static float h= -5, v= 16;

    public static Vector3 dir = new Vector3(h, 0, v);

    // Start is called before the first frame update
    void Start()
    {
        body = GetComponent<Rigidbody>();
        //this.transform.Translate(dir);
        this.transform.position = dir;
    }


    // 이동 관련 함수를 짤 때는 Update보다 FixedUpdate가 더 효율이 좋다고 한다. 그래서 사용했다.
    void FixedUpdate()
    {

        h = Input.GetAxis("Horizontal");
        v = Input.GetAxis("Vertical");

        //Vector3 dir = new Vector3(h, 0, v); // new Vector3(h, 0, v)가 자주 쓰이게 되었으므로 dir이라는 변수에 넣고 향후 편하게 사용할 수 있게 함

        transform.Translate(Vector3.forward * Speed * v * Time.deltaTime);      //이동
        transform.Rotate(Vector3.up * rotateSpeed * h);    // 회전

      
    }

    private void Update()
    {
        dir = this.transform.position;
    }

}
```

```C#
    private bool isWalking; // 걷는지, 안 걷는지 판별
    private float applySpeed;


    private bool isRunning; // 달리는지 판별
    private bool isDead;

    [SerializeField] private float walkTime;  // 걷기 시간
    [SerializeField] private float waitTime;  // 대기 시간
    private float currentTime;

    // 필요한 컴포넌트
    [SerializeField] private Animator anim;
    [SerializeField] private Rigidbody rigidl;
    [SerializeField] private BoxCollider boxCol;

    void Start()
    {
        currentTime = waitTime;   // 대기 시작
        isAction = true;   // 대기도 행동
    }

    void Update()
    {
        Move();
        Rotation();
        ElapseTime();
    }

    private void Move()
    {
        if (isWalking)
            rigidl.MovePosition(transform.position + transform.forward * walkSpeed * Time.deltaTime);
    }

    private void Rotation()
    {
        if (isWalking)
        {
            Vector3 _rotation = Vector3.Lerp(transform.eulerAngles, direction, 0.01f);
            rigidl.MoveRotation(Quaternion.Euler(_rotation));
        }
    }

    private void ElapseTime()
    {
        if (isAction)
        {
            currentTime -= Time.deltaTime;
            if (currentTime <= 0)  // 랜덤하게 다음 행동을 개시
                ReSet();
        }
    }

    private void ReSet()  // 다음 행동 준비
    {
        isWalking = false;
        isAction = true;
        anim.SetBool("Walking", isWalking);

        direction.Set(0f, Random.Range(0f, 360f), 0f);

        RandomAction();
    }

    private void RandomAction()
    {
        int _random = Random.Range(0, 2); // 대기, 풀뜯기, 두리번, 걷기

        if (_random == 0)
            Wait();
    
        else if (_random == 1)
            TryWalk();
    }

    private void Wait()  // 대기
    {
        currentTime = waitTime;
        Debug.Log("대기");
    }


    private void TryWalk()  // 걷기
    {
        currentTime = walkTime;
        isWalking = true;
        anim.SetBool("Walking", isWalking);
        Debug.Log("걷기");
    }
     private void Run(Vector3 _targetPos)
    {
        direction = Quaternion.LookRotation(transform.position - _targetPos).eulerAngles;

        currentTime = runTime;
        isWalking = false;
        isRunning = true;
        applySpeed = runSpeed;

        anim.SetBool("Walking", isRunning);
    }

    public void Damage(int _dmg, Vector3 _targetPos)
    {
        if(!isDead)
        {
            hp -= _dmg;

            if (hp <= 0)
            {
                Dead();
                return;
            }

      
            Run(_targetPos);
        }
    }

    private void Dead()
    {
       
        isWalking = false;
        isRunning = false;
        isDead = true;

        anim.SetTrigger("Dead");
    }



}
```

###게임 개발 스크린샷
![image](https://user-images.githubusercontent.com/101697176/190358837-df3a540d-4117-4921-b422-ade7ee23f96f.png)

![image](https://user-images.githubusercontent.com/101697176/190358897-53e7827f-23c9-473b-b4b5-db1b54b5e921.png)
