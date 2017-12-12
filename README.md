### 3. 오늘의 목표 CRUD

> **오늘은 C, R 까지만 !**
>
> **CRUD**는 대부분의 [컴퓨터](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0) [소프트웨어](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4)가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 묶어서 일컫는 말이다. 사용자 인터페이스가 갖추어야 할 기능(정보의 참조/검색/갱신)을 가리키는 용어로서도 사용된다.
>
> 각 문자는 다음과 같이 표준 SQL문으로 대응 가능하다.
>
> | 이름                 | 조작        | SQL    |
> | ------------------ | --------- | ------ |
> | Create             | 생성        | INSERT |
> | Read(또는 Retrieve)  | 읽기(또는 인출) | SELECT |
> | Update             | 갱신        | UPDATE |
> | Delete(또는 Destroy) | 삭제(또는 파괴) | DELETE |
>
> **ORM(객체 관계 매핑) 이란** ? (Object-Relational Mappings)
>
> ORM이라는 것을 단순하게 표현해보자면 객체와 관계와의 설정? 정도일까? 그럼 여기서 말하는 객체라는 것은 우리가 흔히 말하는 OOP(Object-Oriented Programming)의 그 객체를 이야기 하는 것 이라면, 과연 관계라는 것이 의미하는 것은 무엇일까? 뭐 지극히 기초적인 이야기지만 우리(개발자)가 흔히 사용하고 있는 관계형 데이터베이스를 의미한다.
>
> #### 결론 ! 코드를 통해서 데이터베이스를 조작하는 것 ! SQL을 사용하지 않고 !
>
> #### list = [] 와 list = Array.new 의 차이 / Hash.new 와 hash {} 의 차이
>
> ```
> list = []
> list = Array.new
>
> puts list.class
> # 결과값은 Array
>
> count = Hash.new(0)
> # 디폴트값 생성자로 만든다.
> # 이건 {"1key"=>0}으로 구성이 됨! 즉 초기값은 0 !
> count["1key"] += 1
> count["2key"] += 1
> count["3key"] += 1
>
> puts count
> # 결과값 {"1key"=>1, "2key"=>1, "3key"=>1}
>
> But ! 
> count = {}
> # 이건 {"1key"=>nil}로 구성이 됨
> count["1key"] += 1
> count["2key"] += 1
> count["3key"] += 1
>
> puts count
> # 결과값 hash.rb:10:in `<main>': undefined method `+' for nil:NilClass (NoMethodError)
> # 초기값이 nil이기 때문에 에러가 남.
> ```
>
> #### hash 루프 돌리기
>
> ```
> phone_number = ["01012345678", "01023456789", "01022222222"]
> phone_book = {
>   "이강호" => "01012345678",
>   "강동주" => "01023456789",
>   "김탁희" => "01022222222"
> }
>
> phone_book.each do |key, value|
>   #키만 출력
>   puts key
>   #값만 출력
>   puts value
> end
> ```
>
> #### 1. 투표앱 만들기
>
> - **CRUD 에서 C, R 이용**
>
> **1] app.rb 코드 일부 !**
>
> ```
> # 랜딩페이지 만들기
> # 투표앱 [C,R Create, Read]
> #
> # 1. 사람 이름, 투표한 것
> # - action = '/vote'
> # 2. vote.csv 누가 어떻게 투표했는지 저장시켜 나감
> # - 'vote.csv'에 결과를 쓴다.
> # 3.'/result'
> # - 'vote.csv'에 있는 결과물을 보여준다.
>
> # 투표 참여시 화면
> get '/vote' do
>   @name = params["name"]
>   @vote = params["vote"]
>
>   # CSV 로깅파일 만들기.
>   CSV.open("vote.csv", "a") do |csv|
>     csv << [@name, @vote]
>   end
>
>   erb :vote
> end
>
> # 투표 결과 화면
> get '/result' do
>   @list = Array.new
>   # @list = [] 와 동일한 코드임.
>
>   # @count = {} 와 동일한 코드임.
>   @count = Hash.new(0)
>
>   CSV.foreach("vote.csv") do |row|
>     @list << row
>     # [[name, vote],[name2, vote2]] 저장
>     
>     # 해쉬를 이용하여 카운트하기
>     # 특정시간 +1 로 투표 인원을 구한다 !
>     if row[1] == "1"
>       @count["1hour"] += 1
>     elsif row[1] == "2"
>       @count["2hour"] += 1
>     elsif row[1] == "3"
>       @count["3hour"] += 1
>     end
>   end
>
>   erb :result
> end
> # 전체코드는 voting에서 확인 !
> ```
>
> **2] result.erb 코드 일부!**
>
> ```
> <html>
>   <head>
>     <meta charset="utf-8">
>     <title>Voting</title>
>   <body>
>     <h1>투표 결과</h1>
>       <% @list.each do |row| %>
>         <p><%= row[0] %>님은 <%= row[1] %>에 투표하셨습니다.</p>
>       <% end %>
>     <h1>---------------------------------------------------</h1>
>     <h1>투표결과</h1>
>     
>     <!--해쉬를 이용한 투표결과  -->
>     <!--해쉬를 이용해서 특정시간에 투표자를 +1를 하여 인원 수를 센다-->
>       <p>1시간에 투표한 사람 : <%=@count["1hour"]%></p>
>       <p>2시간에 투표한 사람 : <%=@count["2hour"]%></p>
>       <p>3시간에 투표한 사람 : <%=@count["3hour"]%></p>
>   </body>
>   </head>
> </html>
> ```