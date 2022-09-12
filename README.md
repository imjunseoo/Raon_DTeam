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
> 조작법은 WASD를 기본으로 LShift 달리기, Space바 점프로 구상되어있습니다.
### Player Move 코드
```
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
