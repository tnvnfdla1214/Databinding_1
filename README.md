# Kotlin Android Extensions,Viewbinding,databinding

## 이글은 "멧돼지 개발 블로그"를 보고 작성하였습니다.


![image](https://user-images.githubusercontent.com/48902047/148077668-412518da-ca50-4598-88e3-a9a536414013.png)
<kotlin android extension이 deprecated 되었다고 그만쓰시오>

이렇게 Kotlin Android Extensions이 deprecated 되었다며 대체 할거로 viewbinding이나 databinding을 이용하라고 합니다. 그래서 Viewbinding,databinding에 대해 베이직 하게 알아보려 합니다.

## 왜 사용하는가
xml 파일의 뷰들을 java파일에서 이벤트 처리를 위해 사용하려면 

메모리에 올라와있어야 하는데 그래서 액티비티의 onCreate() 에서 setContentView()를 통해 인플레이션을 합니다.

인플레이션이란 xml 레이아웃 파일 안에 들어있는 뷰 태그들을 이용해 뷰 객체를 메모리에 만드는 과정 즉 자바파일에서 xml을 만질수 있도록 연결해주는것이라 대략적으로 생각하면 될것같습니다.

![image](https://user-images.githubusercontent.com/48902047/148078036-d45796fe-658f-433e-8665-6ba26b1df482.png)

이렇게  xml 레이아웃 파일의 내용을 메모리에 객체로 만들고 소스코드에서 그 객체들을 찾아서 이용하는것입니다.

이때 객체는 findViewByid 메서드를 이용하여 찾고 xml에서 만든 id를 파라미터로 사용합니다.

## Kotlin Android Extensions은 왜 사용하지 않는가

kotlin android extensions 을 4.1버전 이후 기본 플러그인에서 삭제되었고 deprecated 되었다고 사용하지 말라고 합니다.  우선 장단점을 봅시다.

### 장점

1.간편하다 : 그냥 플러그인에 한줄띡 추가하면 findViewById 일일이 할필요없이 편해집니다.

![image](https://user-images.githubusercontent.com/48902047/148078265-73f2358a-2704-401a-b227-c72d4f159a9b.png)

그냥 이거 한줄 추가하면 id를 바로 가져다 쓸수있습니다.

2. 재사용성 : 내부적으로 캐싱을 통해 재사용성을 높인다고합니다.

### 단점

#### 1. RecyclerView의 ViewHolder 사용 시 문제점

사실 findViewById()는 똑같은 코드를 계속 써줘야 한다는 귀찮음도 있지만, 해당 메소드를 자주 호출하면 성능 저하의 문제점도 있습니다. 그런데 RecyclerView에서 KAE를 쓰고 java로 디컴파일했더니 findViewById()를 쓰고 있었습니다.

결국 RecyclerView 에서는 KAE를 사용하면 내부적으로는 findViewById() 를 계속 사용한다는 것이고 성능이 저하된다는것입니다.

#### 2.뷰가 동일한 id를 가지는 경우 상황이 애매해진다

같은 id를 사용하는 view가 있을 경우 혼선이 올수있습니다.

이렇듯 문제점이 있기에 이제 더이상 사용하지 않고 대체제로 Viewbinding이나 Databinding을 씁니다.

## 그래서 Viewbinding이 뭔데 ?
그래서 안드로이드에서는 kotlin android extensions의 대체제로써 Viewbinding을 추천하고 있습니다.

### View Binding이란?

뷰 바인딩(View Binding) 은 뷰와 상호 작용하는 코드를보다 쉽게 작성할 수있는 기능입니다. 모듈의 build.gradle에서 뷰 바인딩 속성이 활성화되면 해당 모듈에있는 각 XML 레이아웃 파일에 대한 바인딩 클래스가 자동으로 생성됩니다. 바인딩 클래스 인스턴스에는 해당 레이아웃에 ID가 있는 모든 뷰에 대해 직접적으로 참조됩니다.

대부분 경우에 있어, 뷰 바인딩을 사용하는 것으로 findViewById 메서드를 대체할 수 있습니다.

한마디로 뷰바인딩을 활성화시키면 바인딩클래스를 안드로이드가 만들어주고 그것을 id를 통해 직접적으로 이용할수있는것이다. 이걸로 findViewById를 대체할 수 있습니다.

### 사용법

#### 세팅하기
우선 build gradle(module)에 아래와 같이 추가해줍니다.

![image](https://user-images.githubusercontent.com/48902047/148078952-2b534e3f-5683-4a4d-8ea4-4c77cb806d74.png)

아래와 같이 바뀌었다고 합니다.

![image](https://user-images.githubusercontent.com/48902047/148079030-8d41c305-8dbc-4d35-8419-a20b31da8433.png)

#### 레이아웃 추가

![image](https://user-images.githubusercontent.com/48902047/148079144-0810c177-e638-44d0-b573-803a23a51eab.png)

또한 바인딩 클래스를 생성하는 동안 레이아웃 파일을 무시하려면 tools : viewBindingIgnore = “true” 속성을 해당 레이아웃 파일의 최상단 루트에 추가해야합니다.

이제 안드로이드가 바인딩 클래스를 자동으로 생성해주는데 거기에는 루트뷰 및 Id가 있는 모든 뷰의 참조가 포함됩니다.

이제 사용을위해 이름을 알아야할것입니다.

그이름이 중요한데  XML 파일의 이름을 Camel case로 변환하고 접미어 “Binding” 을 추가하면 됩니다.

예시로 레이아웃 파일 테스트 위해만든 MainActivity의 xml 파일이름은 activity_main이므로

이것의 바인딩 클래스의 이름은 ActivityMainBinding입니다.

고로 생성된 바인딩 클래스의 인스턴스를 얻으려면 inflate() 메서드를 호출합니다.

그후 setContentView()에 매개변수로 루트뷰(xml파일에서 가장 상위에있는 레이아웃)를 전달하여 화면에서 활성화된 뷰를 만들면 됩니다.

#### 액티비티세팅

![image](https://user-images.githubusercontent.com/48902047/148079471-26f8ec48-a360-457d-bbec-ba96fc36990b.png)

이제 바인딩 클래스의 인스턴스로 모든뷰를 참조할수있습니다.

![image](https://user-images.githubusercontent.com/48902047/148079557-7a5763ee-f708-480d-931f-4b95d18d25b1.png)

이렇게 findViewById 없이도 뷰에 접근가능합니다.

#### 프레그먼트 세팅

마찬가지로 적용해주면됩니다.

최초의 프래그먼트가 아래 상태라면

![image](https://user-images.githubusercontent.com/48902047/148079666-6a4968f3-9066-4cd8-8499-df711b4d54a1.png)

이런식으로 적용하면 됩니다.

![image](https://user-images.githubusercontent.com/48902047/148079784-abc1bb16-2ca6-4e86-ac32-6a48e89f21d9.png)

이거는 대략적인 기본코드를 비교해서 보여주기 위한것이고 실제로 사용할때는 kotlin property를 활용 binding변수의 getter을 정의하며 사용합니다.

![image](https://user-images.githubusercontent.com/48902047/148079834-1a356f20-3e3d-4258-b759-26065d14a17f.png)

그리고 프래그먼트가 메모리에서 날아갈때 즉 사라질때 바인딩 인스턴스도 날려줘야 합니다. 바인딩 인스턴스도 날려버리기위한 코드도 추가해줍니다.

![image](https://user-images.githubusercontent.com/48902047/148079887-05c5d5d6-6460-4354-9e59-68402320b3bc.png)

이렇게 onDestoryView시점에 담겨있던 액티비티가 없어질때

binding에 담아놨던 바인딩 인스턴스도 null값으로 덮어버려 메모리를 날려버립니다.

이렇게 뷰바인딩을 통해 바인딩클래스를 가져와서 바로바로 사용할수있는 코틀린코드에서 뷰를 참조할수있는 방법을 알아보았습니다.

## DataBinding과의 차이?

일단, 둘은 모두 뷰를 직접 참조하는데 사용할 수 있는 binding class 를 제공합니다.

확실히 viewbinding은 보다 단순한 처리의 경우 적합합니다.

### 장점
1. 더 빠른 컴파일 = annotation처리 필요 없음으로 컴파일이 빠릅니다.
2. 사용 편의 - tag처리된 xml 불 필요로 사용 용이합니다. (모듈에서 binding 사용 설정만을 통해 자동으로 모든 레이아웃의 binding class 생성)
 

### DataBinding과의 차이에서 오는 단점?
1. layout에서의 표현식 혹은 변수 제공하지 않으므로 동적인 UI 콘텐츠 생성 할 수 없습니다.
2. two-way- data binding 제공 안합니다. (ex. bindingAdapter)

## 이제 데이터 바인딩을 살펴보자

데이터 바인딩 또한 xml과 데이터를 직접연결해서 view를 코드에서 만지지는 용도 + 데이터를 바인딩(묶다) 즉 선언적으로 뷰에다가 데이터를 꽃아 버리는것입니다.

vue에서 html에다가 그냥 직빵으로 변수 집어넣는것처럼 xml상에서 view에다가 변수를 연결시켜 버리면 그 변수 값에따라 화면단에서 데이터를 표시해줍니다.

vue에서처럼 데이터를 바꾸면 실시간으로 화면단까지 바뀌는것은 livedata와 함께 쓰면 구현할수있습니다. 우선은 글이 너무 길어지니 그것은 다음글에서 다뤄보도록 하겠습니다.

***생각해보자 vue에서 데이터 바뀌면 화면도 같이바뀌니 얼마나 편했던가 그걸 직접만들어야 하는게 화가나지만 어쨋든 할수있음에 감사하자 - 멧돼지님***

databinding 의 장점을 살펴보면

데이터 소스가 변경될때 ui 새로고침에 대해서 신경안써도되고(물론 라이브데이터 같이써야함)

ui호출하는 코드 없어지니 코드가 간결해지고

앱의 성능을 개선하고 메모리 누수 및 nullpointerexception 을 방지하는데 도움됩니다.

### 사용법

#### 세팅하기

![image](https://user-images.githubusercontent.com/48902047/148080651-3488d96b-d9ff-483f-8f91-d5021b1f03c8.png)

위와 같이 dataBinding을 추가해주면 아래 사진처럼 kotlin-kapt plugin을 설정하라고 나옵니다.

![image](https://user-images.githubusercontent.com/48902047/148080683-30a26980-ac65-466f-b8df-5aa9572ea67f.png)

사진처럼 안내 문구가 뜬다면 맨위쪽에 아래 코드를 추가합니다.

![image](https://user-images.githubusercontent.com/48902047/148080712-47ce8a2f-0907-4539-aa42-b30b8545c9a7.png)


#### 레이아웃

data binding 사용하려면 xml 리소스는 \<layout>태그를 루트태그로 가져야합니다.

고로 기존의 있던 내용들을 \<layout>태그로 감싸줍니다.

![image](https://user-images.githubusercontent.com/48902047/148080821-77217090-68d0-40ba-ad0a-42095b8b3206.png)

#### 액티비티

![image](https://user-images.githubusercontent.com/48902047/148081087-f8dc7c62-f0df-4f10-854c-dacd3bfd07c8.png)

뷰바인딩처럼 카멜케이스로 똑같은 이름의 객체 생성되는데 그 자료형으로 변수 만들고

거기에 DataBindingUtil.setContentView(this, 액티비티에 맞는 레이아웃) 이 함수를 통해 setContentView를 대체해줍니다.

#### view에서 보여줄 것들을 담은 dataclass 선언(생략가능)

데이터 클래스로 데이터 모아놓고 관리할꺼면 data class를 만들어준다 필요한것들 맞춰서

![image](https://user-images.githubusercontent.com/48902047/148081245-753ac18b-4ac4-4675-96e3-a5b446202760.png)

#### xml 파일에서 data 정의

이제 xml파일에서 \<data>요소를 작성해줍니다.

\<data> 태그는 \<layout> 에서 사용할 변수를 정의하는데 사용됩니다.

name은 이제 여기안에서 xml안에서 변수 이름을 뜻하는것이라 생각하면되고

좀있다가 코틀린파일에서 binding.name에 정의한거 = 데이터클래스 혹은 this 를통해서 데이터를 연결시켜서 xml에서 꺼내 쓸수있게 해주는것입니다.

type은 이제 자료형 느낌으로 data class를 이용한다면 그 클래스나 아니면 액티비티를 직접 연결시켜서 어느 클래스를 가져다 쓸거냐를 적으면 됩니다. 적는어야하는 내용은 (내 패키지명 + 액티비티 명 또는 프래그먼트 명) 입니다.

![image](https://user-images.githubusercontent.com/48902047/148081720-0c337368-f29c-40c9-8aa9-bb64afd3f2ee.png)

이글은 자기소개에 대한 내용이었기에 name를 introduce로 했고

type은 data class를 따로 만들어서 데이터를 사용했기에 데이터 클래스의 패키지명+클래스명을 넣어줬습니다.

만약 액티비티에 바로 변수 선언하고 그거사용할꺼면

![image](https://user-images.githubusercontent.com/48902047/148081807-c3a19c1e-4720-447b-87b1-75657c1dea8d.png)

이런식으로 액티비티를 넣어줘도 됩니다.

#### Activity에 dataclass 초기화 및 binding객체에 dataSource set하기

데이터 클래스를 초기화 시키고  그것을 binding 객체에 넣어주어서 연결시켜줍니다.

![image](https://user-images.githubusercontent.com/48902047/148081896-8d56c3f4-e72c-40e9-9de7-bac6c50da1ee.png)

그리고 만약 data class안쓰고 바로 액티비티에 변수 선언해주고 그거 사용하고싶다면

![image](https://user-images.githubusercontent.com/48902047/148081944-58d2f905-058c-4af6-b9e0-e7ce996889c2.png)

이렇게 액티비티에서 변수 만들고

binding객체에는 this를 넣어줍니다.

#### xml에 원하는 뷰들에 선언하기

![image](https://user-images.githubusercontent.com/48902047/148082009-847347fc-1ce0-4a10-b34f-3a4b3e323e84.png)

이제 마지막으로 text같이 데이터가 연결되는곳에 name으로 지정했던 introduce.변수명 을 @{} 로 감싸서 데이터를 뷰에 연결시켜버립니다. 이미지의 표현식은 변수와 함께 문자열도 표현하고싶을때 @{`문자열`+변수명}으로 표현해주면 됩니다.

이렇게 databinding의 간단한 사용법을 알아보았습니다.

#### 프래그먼트에서 databinding
Fragment에서 데이터 바인딩을 사용할 때는 Activity와 거의 같지만 binding을 받아오는 코드가 살짝 다릅니다. 기존에 inflater로 inflate하는 방식과 유사한 방식으로 가져오면 됩니다.

```kotlin
// Activity
val binding = DataBindingUtil.setContentView(this, layoutResourceId)

// Fragment
val binding = DataBindingUtil.inflate(inflater, layoutResourceId, container, false)
```

또한 데이터 바인딩 또한 Fragment 에서 사용할때는 라이프 사이클과 관련하여 메모리 누수가 발생할수있기에 뷰바인딩에서 프래그먼트에 적용한것처럼  onDestroyView 에서 null값을 넣을수있도록 만들어줘야합니다.

```kotlin
class CameraFragment : Fragment() {
	private var _binding : FragmentCameraBinding? = null
    private val binding get() = _binding!!
    
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = DataBindingUtil.inflate(inflater,R.layout.fragment_camera, container, false)
         return binding.root
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```
이런식으로 작성해줘야 메모리 누수를 막을수있습니다.
