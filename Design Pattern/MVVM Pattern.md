
### MVVM 패턴
=========

### 💡MVVM을 사용하는 건 “seperation of concerns”를 달성하기 위해서이다.
---

[MVVM 패턴의 개요]

MVVM 패턴은 유저 인터페이스를 구현할 수 있는 디자인 패턴입니다. 이 패턴은 애플리케이션을  Model, View, ViewModel로 나누어 구성합니다.


- view
    1. view란 사용자와 `즉각적인` 반응을 하는 user interface이다.
        1. ex) Activity, Fragment
    2.  iOS는 ViewController까지 View가 된다.
    
    특징
    
    1. `no business logic` 
        1. DB와 직접적으로 통신하면 안 된다.
    2. 역할
        1. ViewModel에서 정보를 가져 온 정보를 화면에 띄운다
        2. Android 특정 작업을 수행한다.
        3. 사용자와 상호 작용하는 이벤트를 ViewModel에 전달한다.

- ViewModel
    1. View와 business logic 사이의 접착제이다.
    2. 특징
        1. lifecycle이 없다.
            
            →보통 lifecycle이 있는 livedata와 함께 사용한다.
            
    3. 역할
        1. Repository에서 data를 가져와 View에 전달한다. 
        2. View는 ViewModel을 직접 참조하지만, ViewModel은 View를 직접 참조하지 않는다.
            
            <aside>
            ✅ →즉, ViewModel은 어떤 View가 자신을 참조하고 있는지 알지 못 한다.
            
            </aside>
            
    
- View와 ViewModel 그리고 **Data Binding**
    1. View와 ViewModel은 `선언적 데이터 바인딩` 상태이다.
        
        ```xml
        <TextView android:text="@{viewmodel.userName}" />
        ```
        
        1. 선언적 : 미리 사용목표가 명시됨.
        2. iOS에서 데이터 바인딩을 하는 방법(??)
            - KVO
            - Delegation
            - Functional Reactive Programming
            - Property Observer
    2. **Data Binding** 
        
        <aside>
        💡  Model과 UI 요소 간의 싱크를 맞춰주는 것. 이를 통해 View와 Logic이 분리되어 있어도 한 쪽이 바뀌면 다른 쪽도 업데이트가 이루어져 데이터의 일관성을 유지할 수 있습니다.
        
        </aside>
        
        1. View에서 미리 사용 목표를 명시한다.
        2. ViewModel에서 이를 연결함으로써 데이터를 동기화한다. 
            
            →이렇게 함으로써 코드양이 적어지고 각 부분의 역할이 명확해진다.
            
    3. **LiveData(obsevable한 데이터)**
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d5e269d8-27ce-4650-84da-0684e1e9ef42/Untitled.png)
        
        <aside>
        💡 VIewModel은 View에게 알맞은 데이터를 전달하기 위해 **LiveData**를 ****사용한다.
        
        </aside>
        
        1. 특징
            1. observable 한 데이터이다.
            2. 수명 주기 인식 라이브러리이다.
            3. 특징2 덕분에, 수명 주기를 직접 관리할 필요가 없다. 
                
                →Activity나 Fragmentrk 파괴된 경우, observer에게 자동으로 알리지 않는다.
                
        2. 적용 방법
            1. View가 ViewModel을 직접 참조
            2. ViewModel은 관찰 가능한 data를 노출 시킴
            3. View가 직접 참조한 ViewModel의 data를 관찰 함. 
            4. ViewModel의 data가 변경되면 이를 관찰하는 View에 변경에 대한 알림이 전송 됨.
                
                **→data가 갱신 되었을 때 ViewModel이 View를 직접 갱신할 필요가 없다.**
                
    4. 이벤트 실행 전달 방식
        1. View에서 UI와 사용자 간의 상호 작용이 발생한다.
        2. View는 ViewModel에 `Command 패턴`의 형태로 이벤트들이 실행되어야 함을 전달한다.
    
      
    
- Model
    
    <aside>
    💡 business code를 넣는 곳이다.
    
    </aside>
    
    1. Repository
        
         D. **Local Storage와 Server 사이의 중개자** 
        
        1. ViewModel과 Model사이의 중간 단계에 존재
        2. 역할
            1. 원격 데이터가 local에 cache되어야 하는지 구분한다.
            2. **ViewModels의 단일 소스**이기도 하다.
                
                → ViewModel이 일부 data를 원하면 공급한다.
                
    2. Repository 하위 모든 것(Model포함)들의 특징
        1. 자체 클래스 그룹으로 간주할 수 있다.
        2. 앱의 데이터에 관련하여 동작한다.
        3. Local database나 network에서 data를 가져온다.

- MVVM구성 요소의 연결성
    
    <aside>
    💡 유지 보수를 위해서는 아래의 참조 트리를 준수해야 한다.
    
    </aside>
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/28c9c39d-42f1-4594-bfc8-5fd876da14d8/Untitled.png)
    
    1. **상위 계층은 자식에 대한 직접 참조**를 한다.
    2. 자식은 부모에 대한 참조가 없다.
    3. **자식**은 LiveData 혹은 다른 라이브러리를 통해 부모에게 **observable한 데이터를 제공**한다.
    
- 동작 구조
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ce1341f-1d4f-4dbc-ac59-fbd97aab45cb/Untitled.png)
    
    1. 사용자의 Action이 View를 통해 들어온다.
    2. 들어온 Action은 Command 패턴으로 View Model에 전달 된다.
    3. View Model은 Model에게 데이터를 요청한다.
    4. Model은 View Model에게 요청받은 데이터를 반환한다.
    5. View Model은 전달 받은 데이터를 가공하여 저장한다.
    6. View는 View Model간의 Data Binding을 통해 자동의 View가 갱신 된다.
    
- MVC 패턴의 장점
    1. View와 Model이 서로 전혀 알지 못하기에 독립성을 유지할 수 있다.
    2. 독립성을 유지하기 때문에 효율적인 유닛테스트가 가능하다.
    3. View와 ViewModel을 바인딩하기 때문에 코드의 양이 줄어든다.
    4. MVVM 패턴은 View와 Model 사이의 의존성이 없습니다.
    5. Command 패턴과 Data Binding을 사용하여 View와 View Model 사이의 의존성이 없기 때문에 각각의 부분은 독립적이기 때문에 모듈화 하여 개발할 수 있다.

- MVC패턴의 단점
    1. ViewModel 설계가 어렵다. 간단한 서비스를 구현할 때는 오히려 비효율적인 일이 된다.
    2. 데이터 바인딩이 필수적으로 요구된다.
    3. MVC 패턴의 한계처럼 복잡해질수록 ViewModel이 빠르게 커진다.
    4. 표준화 된 틀이 존재하지 않아 사람마다 이해가 다르다.
 
 
