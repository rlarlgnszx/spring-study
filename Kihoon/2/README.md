# 스프링 핵심원리 이해-part1

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled.png)

# 1. 비즈니스 요구사항과 설계

- 회원
    - 가입 , 조회
    - 일반, vip
    - 자체 db , 혹은 외부 시스템연결
- 주문과 할인
    - 회원 상품주문가능
    - 등급에 따라 할인 정책 적용
    - VIP는 1000원 할인해주는 고정금액할인 적용(변경가능)
    - 할인정책은 변경가능성 높음

### 문제점 :

- 회원데이터, 할인정책 현재 결정하기 어려움
- 즉 인터페이스로 만들고 바뀔수있도록 구현하기

# 2. 회원 도메인 설계

## 회원도메인 협력 관계

- 역할과 구현

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%201.png)

- 회원 저장소라는 계층을 따뤄두어 다른 DB의 형태라도 접근할수있는 방식 (JPA와 비슷한개념)

## 회원 클래스 다이어그램

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%202.png)

- Impl이란?
    - 회원서비스라는 인터페이스를 구현한 구현객체에 Impl을 붙여 구현하는것

## 회원 객체 다이어그램

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%203.png)

- 서버 실행없이 클래스만 분석한 것

## 회원 도메인 설계의 문제점

- 다른 저장소로 변경시 OCP 가능한가?
- DIP는 잘 지키고 있는가??
- 의존관계가 인터페이스와 구현모두 의존하게 된다.
    - 무슨말일까?
    - service를 의존하는 구현체가 repository를 구현한 구현체를 의존하게 됨
    - 즉 memberservice impl은 memberRepo와 MemorymemberRepo 둘다 의존함
    
    ![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%204.png)
    
    - 뭐죠 ? 그럼 어떻게 써야하죠 ? DIP위반

## 주문과 할인 도메인 설계

- 회원 상품주문가능
- 등급에 따라 할인 정책 적용
- VIP는 1000원 할인해주는 고정금액할인 적용(변경가능)
- 할인정책은 변경가능성 높음

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%205.png)

1. 주문 생성: 클라이언트가 주문 요청
2. 회원 조회 : 할인을 위해서 클라이언트 회원등급 , 따라서 주문서비스는 회원저장소에서 회원 조회
3. 할인 적용 : 회원등급에 따른 할인여부를 “할인정책에 위임”
4. 주문 결과 반환 : 주문서비스는 할인 결과를 포함한 주문 결과를 반환

## 도메인 전체를 그릴시에

- 역할 먼저 !
- 구현 그다음에!

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%206.png)

- 역할과 구현을 분리해서 자유롭게 객체 조립할수 있게 설계
- 덕분에 자유롭게 “회원 저장소” , “할인 정책” 등을 유연하게 변경가능
- 왜? 회원 저장소
    - DB가 계속 바뀔수 있으니
- 왜? 할인정책
    - 할인정책이 계속 바뀌니깐

## 객체 레벨로 구현 한 클래스 다이어그램

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%207.png)

## 주문 도메인 객체 다이어그램

- new 해서 생성한 application에서의 동작과정

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%208.png)

- 여기서 메모리 저장소가 바뀌거나 , 할인정책이 바뀌어도 이러한 객체 다이어그램 그대로 유지가능
- 협력관계 그대로 사용가능합니다.

![Untitled](%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%8B%E1%85%B5%E1%84%92%E1%85%A2-part1%20e697cbe4b7054684a6e60366f317b9d4/Untitled%209.png)