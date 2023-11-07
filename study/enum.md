# Enum 기초 활용법

`private final static`을 사용하는 대신 `enum`이라는 클래스를 알게 되어 사용하는데, <br>
`enum`을 사용함으로서 얻게 되는 장점을 정확하게 알지 못한 채 사용하고 있다는 생각이 들어 이번 기회로 확실하게 공부하려고 합니다.

## enum의 장점

* 문자열과 비교해, **IDE의 적극적인 지원**을 받을 수 있습니다.
    * 자동완성, 오타검증, 텍스트 리팩토링 등
* 허용 가능한 값들을 제한할 수 있습니다.
* 리팩토링 시 변경 범위가 최소화 됩니다.
    * 내용 추가가 필요하더라, Enum 코드 외 수정할 필요가 없습니다.
* 코드가 단순해지며 가독성이 좋아집니다.
* 인스턴스 생성, 상속을 방지하며 상수 값의 타입 안전성이 보장됩니다.
* enum이라는 키워드로 열거형의 의도를 명확히 드러냅니다.
<br><br>

위 장점들은 모든 언어들의 Enum에서 얻을 수 있는 공통된 장점입니다. <br>
하지만 Java의 Enum은 이보다 더 많은 장점을 가지고 있습니다. <br>
C / C++의 경우 Enum이 결국 int 값이지만, Java의 Enum은 완전한 기능을 갖춘 클래스이기 때문입니다. <br>

## enum이 나온 배경

1. 코드에 주석이 없을 경우 코드 이해가 어렵습니다.
    ```java
    int type = 1;
    if(type == 1){
        ...
    } else {
        ...
    }
    ```
    type 에 대한 주석이나 if문 안에서 처리하는 내용에 대한 주석이 없으면 코드 이해가 어렵습니다. <br>

2. `final static` 으로 설정하기
    ```java
    private final static int BOY = 1;
    private final static int GIRL = 2;

    private final static int MONDAY = 1;
    private final static int TUESDAY = 2;
    ```
    그러나 같은 상수명을 갖는 다른 의미의 값이 존재하거나 다른 상수명이지만 같은 값을 가지는 경우가 있을 수 있고 에러가 발생할 수 있습니다. <br>
    각각의 상수들은 자신을 인스턴스화 한 값을 할당합니다. <br>

    BOY 와 MONDAY 는 서로 다른 데이터를 의미하는데 비교할 경우
    ```java
    if(BOY == MONDAY){
        ...
    }
    ```
    컴파일 에러가 발생하지 않고 런타임 단계에서 생각지 못한 문제를 발생시킬 수 있습니다. <br>

3. `class` 로 작성 된 상수를 인스턴스화 하여 구체화하기
    ```java
    class SEX {
        public final static SEX BOY = new SEX();
        public final static SEX GIRL = new SEX();
    }

    class DAY {
        public final static DAY MONDAY = new DAY();
        public final static DAY TUESDAY = new DAY();
    }
    ```

    인스턴스화하면 자신의 타입으로 비교하기 때문에 컴파일 시 에러 확인이 가능합니다.

    ```java
    int type = 1;
    switch(type){
        case DAY.MONDAY:
            ...
            break;
        case DAY.TUESDAY:
            ...
            break;
    }
    ```
    그런데 switch문에 사용할 수 없습니다. <br>
    switch문을 사용할 수 있는 데이터 타입이 한정되어 있습니다. <br>
    상수 사용 시 switch문을 사용해야 가독성이 좋기 때문에 static을 사용하는 데 있어 아쉬운 부분이 있었고, <br>
    이 문제점을 해결하기 위해 `enum` 이 나왔습니다.

    ### enum 사용법
    ```java
    enum SEX {
        BOY(1), GIRL(2);

        private int sex;

        SEX(int type){
            sex = type;
        }

        public int getSex() {
            return sex;
        }
    }

    enum DAY {
        MONDAY, TUESDAY
    }
    ```
    Enum 클래스에 상수로 사용할 변수를 작성해줍니다. <br>
    `BOY(1)` 처럼 값을 함께 지정해줄 수 있는데 생성자 파라미터를 통해 필드에 값을 설정할 수 있습니다. <br>
    `getter` 메소드를 통해 값을 가져다 쓸 수 있습니다. <br>

    ```java
    SEX type = SEX.BOY;
    switch (type){
        case BOY:
            ...
            break;
        case GIRL:
            ...
            break;
    }
    ```

## enum 의 목적

`enum`과 `static`을 선언한 Constant는 목적이 다릅니다. <br>
`enum`은 연관된 상수들을 묶어 추상화 시킨 것이고, `constant`는 값의 재할당을 막기 위한 목적입니다.

# Enum 심화 활용법
다음은 **프로젝트를 진행함에 있어 발생한 문제를 Enum을 통해 어떻게 해결했는가**를 소개하는 글입니다.<br>

## 데이터들 간의 연관관계 표현
```java
public class LegacyCase {
    public String toTable1Value(String originValue) {
        if("Y".equals(originValue)){
            return "1";
        } else {
            return "0";
        }
    }
    
    public boolean toTable2Value(String originValue){
        if("Y".equals(originValue)){
            return true;
        } else {
            return false;
        }
    }
}
```
위 코드는 기능 상의 문제는 없지만, 몇 가지 문제가 발생합니다.
* "Y", "1", true 는 **모두 같은 의미** 라는 것을 알 수 없습니다.
* 불필요한 코드량이 많습니다. **if문을 포함한 메소드 단위**로 코드가 증가하게 됩니다.

해당 부분을 Enum으로 추출하면 다음과 같습니다.
```java
public enum TableStatus {
    Y("1", true),
    N("0", false);

    private String table1Value;
    private boolean table2Value;

    TableStatus(String table1Value, boolean table2Value){
        this.table1Value = tableValue;
        this.table2Value = tableValue;
    }

    public String getTable1Value(){
        return table1Value;
    }

    public boolean isTable2Value() {
        return table2Value;
    }
}
```
"Y", "1", true 가 한 묶음으로, "N", "0", false가 한 묶음이 된 것을 코드로 확인할 수 있습니다. <br>
또한 추가 타입이 필요한 경우 Enum 상수와 get 메소드만 추가하면 됩니다. <br>
(만일 lombok의 @Getter 를 사용한다면 Enum의 get 메소드까지 제거되어 더 깔끔한 코드가 됩니다.)<br>

```java
@Test
public void origin테이블_조회한_데이터를_다른_2테이블에_등롭한다() throws Exception {
    // given
    TableStatus origin = selectFromOriginTable();

    // then
    String table1Value = origin.getTable1Value();
    boolean table2Value = origin.isTable2Value();

    assertThat(origin, is(Table1Value.Y));
    assertThat(table1Value, is("1"));
    assertThat(table2Value, is(true));
}
```
TableStatus 라는 Enum 타입을 전달받기만 한다면, 그에 맞춘 table1, table2 값을 바로 얻을 수 있습니다.

## 상태와 행위를 한 곳에서 관리

```java
public class LegacyCalculator {
    public static long calculate(String code, long originValue){
        if("CALC_A".equals(code)){
            return originValue;
        } else if ("CALC_B".equals(code)){
            return originValue * 10;
        } else if ("CALC_C".equals(code)){
            return originValue * 3;
        } else {
            return 0;
        }
    }
}
```
위 코드와 같이 code 값이 "CAL_A"일 경우에 값 그대로, "CALC_B", "CALC_C" 경우 다르게 계산하여 전달해야 합니다. <br>
가장 쉬운 해결 방법은 아래와 같이 static 메소드를 작성하여 필요한 곳에서 호출하는 방식일 것입니다. <br>

```java
@Test
public void 코드에_따라_서로다른_계산하기_기존방식() throws Exception {
    String code = selectCode();
    long originValue = 10000L;
    long result = LegacyCalculator.calculate(code, originValue);

    assertThat(result, is(10000L));
}
```
위 코드를 보면 LegacyCalculator 메소드와 code는 서로 관계가 있음을 코드로 표현할 수 없습니다. <br> 
code는 code 대로 조회하고, 계산은 별도의 메소드를 통해 진행됩니다. <br><br>

뽑아낸 code에 따라 지정된 메소드에서만 계산되길 원하는데, 현재 상태로는 강제할 수단이 없습니다. <br>
지금은 문자열 인자만 받고, Long 타입을 리턴하는 모든 메소드를 사용할 수 있는 상태입니다. <br>
* 똑같은 기능을 하는 메소드를 **중복 생성** 할 수 있습니다.
    * 히스토리가 관리 안된 상태에서 신규화면이 추가되어야 할 경우 계산 메소드가 있다는 것을 몰라 다시 만드는 경우가 빈번합니다.
    * 만약 기존 화면의 계산 로직이 변경될 경우, 신규 인력은 2개의 메소드 로직을 다 변경해야 하는지, 해당 화면만 변경해야 하는지 알 수 없습니다.
    * 관리 포인트가 증가할 확률이 높습니다.

* 계산 메소드를 누락할 수 있습니다.
    * 결국 문자열과 메소드로 분리되어 있기 때문에 이 계산 메소드를 써야함을 알 수 없어 <br>
     새로운 기능 생성 시 계산 메소드 호출이 누락될 수 있습니다.

**`DB의 테이블에서 뽑은 특정 값은 지정된 메소드와 관계가 있습니다.`** <br>
역할과 책임이라는 관점으로 보았을 때, 위 메세지는 Code에 책임이 있다고 생각될 수 있습니다. <br>

```java
public enum CalculatorType {
    CALC_A(value -> value),
    CALC_B(value -> value * 10),
    CALC_C(value -> value * 3),
    CALC_ETC(value -> 0L);

    private Function<Long, Long> expression;

    CalculatorType(Function<Long, Long> expression) {this.expression = expression;}

    public long calculate(long value) { return expression.apply(value); }
}
```
위 enum 클래스를 활용하여 Code가 본인만의 계산식을 갖도록 지정하였습니다. <br>
(Java8 업데이트로 인자값으로 함수 사용이 가능해졌습니다.) <br>
물론 내부적으로는 인터페이스를 사용하지 완전하다고는 할순 없습니다. <br>

(Entity 클래스에 선언할 경우 String이 아닌 enum을 선언하면 됩니다)
```java
@Column
@Enumerated(EnumType.STRING)
private CalculatorType calculatorType;
```
JPA를 사용하는 경우 `@Enumerated(EnumType.STRING)`을 선언하면 Enum 필드가 테이블에 저장 시 숫자형인 1,2,3이 아닌  Enum의 name이 저장됩니다.<br>
그리고 실제 사용하는 곳에서도 이제 직접 code에게 계산을 요청하면 됩니다.
```java
@Test
public void 코드에_따라_서로다른_계산하기_enum() throws Exception {
    CalculatorType code = selectType();
    long originValue = 10000L;
    long result = code.calculate(originValue);

    assertThat(result, is(10000L));
}
```

값(상태)와 메소드(행위)가 어떤 관계가 있는지에 대해 더이상 다른 곳을 찾을 필요가 없게 되었습니다.
코드 내에 전부 표현되어 있고, **Enum 상수에게 직접** 물어보면 되기 때문입니다.

## 데이터 그룹 관리
결제라는 데이터는 **결제 종류**와 **결제 수단**이라는 2가지 형태로 표현됩니다. <br>
예를 들어 신용카드 결제는 **신용카드 결제**라는 결제 수단이며, **카드**라는 결제 종류에 포함됩니다. <br>
이 **카드 결제**는 페이코, 카카오페이 등 여러 결제 수단이 포함되어 있다고 생각하면 좋습니다. <br><br>

결제된 건이 어떤 결제 수단으로 진행되었으며, 해당 결제 방식이 **어느 결제 종류에 속하는지** 확인할 수 있어야만 하는 조건이 있습니다. <br>
이를 해결하는 가장 쉬운 방법은 문자열과 메소드, if문으로 구현하는 것입니다. <br>
```java
public class LegacyPayGroup{
    public static String getPayGroup(String payCode){
        if("ACCOUNT_TRANSFER".equals(payCode) || "REMITTANCE".equals(payCode) || "ON_SITE_PAYMENT".equals(payCode) || "TOSS".equals(payCode)){
            return "CASH";
        } else if ("PAYCO".equals(payCode) || "CARD".equals(payCode) || "KAKAO_PAY".equals(payCode) || "BAEMIN_PAY".equals(payCode)){
            return "CARD";
        } else if ("POINT".equals(payCode) || "COUPON".equals(payCode)){
            return "ETC";
        } else {
            return "EMPTY";
        }
}
```
위 코드에 문제점은 다음과 같습니다.
* 둘의 관계를 파악하기 어렵습니다
    * 위 메소드는 포함관계를 나타내는 것인지? 단순한 대체값을 리턴한 것인지?
    * 현재는 결제 종류가 결제 수단을 포함하고 있는 관계이지만, 메소드만으로 표현이 불가능 합니다.

* 입력값과 결과값 예측이 불가능합니다.
    * 결제 수단의 범위를 지정할 수 없어 문자열이 전부 파라미터로 전달될 수 있습니다.
    * 마찬가지로 결과를 받는 쪽에서도 문자열을 받아 결제 종류로 지정된 값만 받을 수 있도록 검증코드가 필요하게 됩니다.
* 그룹별 기능을 추가하기 어렵습니다.
    * 결제 종류에 따라 추가 기능이 필요한 경우 현재 상태라면 어떻게 구현할 수 있을지?
    * 또 다시 결제 종류에 따른 if문으로 메소드를 실행하는 코드를 작성해야 하는지?

각각의 메소드는 원하는 때에 사용하기 위해 독립적으로 구성할 수 밖에 없는데, <br>
그럴 때마다 결제 종류를 분기하는 코드가 필수적으로 필요하게 됩니다. (이건 좋지 않습니다.) <br><br>

결제 종류, 결제 수단 등의 관계를 명확히 표현하며, 각 타입은 본인이 수행해야 할 기능과 책임만 가질 수 있게 하려면 <br>
기존 방식으로는 해결하기가 어렵다고 생각하였습니다.
```java
public enum PayGroup {
    CASH("현금", Arrays.asList("ACCOUNT_TRANSFER", "REMITTANCE", "ON_SITE_PAYMENT", "TOSS")),
    CARD("카드", Arrays.asList("PAYCO", "CARD", "KAKAO_PAY", "BAEMIN_PAY")),
    ETC("기타", Arrays.asList("POINT", "COUPON")),
    EMPTY("없음", Collections.EMPTY_LIST);

    private String title;
    private List<String> payList;

    PayGroup(String title, List<String> payList) {
        this.title = title;
        this.payList = payList;
    }

    public static PayGroup findByPayCode(String code){
        return Arrays.stream(PayGroup.Values())                 // PayGroup의 Enum 상수들을 순회하며
                .filter(payGroup -> payGroup.hasPayCode(code))  // payCode를 갖고 있는게 있는지 확인합니다. 
                .findAny()
                .orElse(EMPTY);
    }

    public boolean hasPayCode(String code) {
        return payList.stream()
                .anyMatch(pay -> pay.equals(code));
    }

    public String getTitle() { return title; }
}
```
`findByPayCode` 메소드에서 Enum의 상수들을 순회하며 찾습니다. <br>
Java의 Enum은 결국 클래스인 점을 이용하여 Enum의 상수에 결제 종류 문자열 리스트를 갖도록 하였습니다. <br>
각 Enum 상수들은 본인들이 가지고 있는 문자열들을 확인하여 문자열 인자값이 어느 Enum 상수에 포함되어 있는지 확인할 수 있게 되었습니다.
<br><br>
```java
@Test
public void PayGroup에게_직접_결제종류_물어보기_문자열() throws Exception {
    String payCode = selectPaycode();
    PayGroup payGroup = PayGroup.findByPayCode(payCode);

    assertThat(payGroup.name(), is("BAEMIN_PAY"));
    assertThat(payGroup.getTitle(), is("배민페이"));
}
```
결제 수단이 **문자열**이므로 해당 결제 수단 역시 Enum으로 전환해주어야 합니다.
<br><br>
```java
public enum PayType {
    ACCOUNT_TRANSFER("계좌이체"),
    REMITTANCE("무통장입금"),
    ON_SITE_PAYMENT("현장결제"),
    TOSS("토스"),
    PAYCO("페이코"),
    CARD("신용카드"),
    KAKAO_PAY("카카오 페이"),
    BAEMIN_PAY("배민 페이"),
    POINT("포인트"),
    COUPON("쿠폰"),
    EMPTY("없음"),
}
```
Enum으로 결제 종류를 만들고, PayGroup에서 사용할 수 있도록 수정합니다.
```java
public enum PayGroupAdvanced {
    CASH("현금", Arrays.asList(PayType.ACCOUNT_TRANSFER, PayType.REMITTANCE, PayType.ON_SITE_PAYMENT, PayType.TOSS)),
    CARD("카드", Arrays.asList(PayType.PAYCO, PayType.CARD, PayType.KAKAO_PAY, PayType.BAEMIN_PAY)),
    ETC("기타", Arrays.asList(PayType.POINT, PayType.COUPON)),
    EMPTY("없음", Collections.EMPTY_LIST);

    private String title;
    private List<PayType> payList;

    PayGroupAdvanced(String title, List<PayType> payList) {
        this.title = title;
        this.payList = payList;
    }

    public static PayGroupAdvanced findByPayCode(PayType payType){
        return Arrays.stream(PayGroupAdvanced.Values())                 // PayGroup의 Enum 상수들을 순회하며
                .filter(payGroup -> payGroup.hasPayCode(payType))  // payCode를 갖고 있는게 있는지 확인합니다. 
                .findAny()
                .orElse(EMPTY);
    }

    public boolean hasPayCode(PayType payType) {
        return payList.stream()
                .anyMatch(pay -> pay.equals(payType));
    }

    public String getTitle() { return title; }
}
```

이를 사용하는 코드는 아래와 같이 변경됩니다.
```java
@Test
public void PayGroup에게_직접_결제종류_물어보기_PayType() throws Exception {
    PayType payType = selectPayType();
    PayGroupAdvanced payGroupAdvanced = PayGroupAdvanced.findByPayType(payType);

    assertThat(payGroupAdvanced.name(), is("BAEMIN_PAY"));
    assertThat(payGroupAdvanced.getTitle(), is("배민페이"));
}
```

## 관리 주체를 DB에서 객체로
정산 플랫폼은 수많은 카테고리가 존재하기 때문에 UI에서 select box로 표현되는 부분이 많습니다. <br>
![Alt text](/study/img/image.png)
* 코드명만 봐서는 무엇을 나타내는지 알 수 없습니다.
* 항상 코드 테이블 조회 쿼리가 실행되어야만 했습니다.
* 카테고리 코드를 기반으로 한 **서비스 로직을 추가할 때** 그 위치가 애매했습니다.

<br>
특히 카테고리의 경우 6개월에 1~2개가 추가될까 말까한 영역인데 굳이 테이블로 관리하는 것은 장점보다 단점이 더 많다고 판단되었습니다. <br>
(물론 CI가 구축되어 하루에도 몇번씩 배포할 수 있는 상황이기에 가능하다고 생각합니다.)<br>
카테고리성 데이터를 Enum으로 전환하고, 팩토리와 인터페이스 타입을 선언하여 일관된 방식으로 관리되고 사용될 수 있도록 진행하였습니다.<br><br>

Enum을 바로 JSON으로 리턴하게 되면 **상수 name만 출력**됩니다. <br>
저에게 필요한 건 DB의 컬럼값으로 사용될 Enum의 name, View Layer에서 출력될 Title 2개의 값이기 때문에 <br> 
Enum을 인스턴스로 생성하기 위한 클래스 선언이 필요했습니다. <br>
```java
public interface EnumMapperType {
    String getCode();
    String getTitle();
}
```
값을 담을 클래스(VO)는 이 인터페이스를 생성자 인자로 받아 인스턴스를 생성하도록 합니다.
<br>
```java
public class EnumMapperValue {
    private String code;
    private String title;

    public EnumMapperValue(EnumMapperType enumMapperType){
        code = enumMapperType.getCode();
        title = enumMapperType.getTitle();
    }
 
    public String getCode() { return code; }
    public String getTitle() { return title; }

    @Override
    public String toString() {
        return "{" +
                "code='" + code + '\'' +
                ", title='" + title + '\'' +
                '}';
    }
}
```
Enum은 미리 선언한 인터페이스를 구현(implements)만 하면 됩니다.
```java
public enum FeeType implements EnumMapperType{
    PERCENT("정율");
    MONEY("정액");

    private String title;

    FeeType(String title) { this.title = title; }

    @Override
    public String getCode(){ return name(); }

    @Override
    public String getTitle(){ return title; }
}
```
이제 필요한 곳에서 Enum을 Value 클래스로 변환 후 전달하기만 하면 됩니다.
```java
@GetMapping("/no-bean-categories")
public List<EnumMapperValue> getNoBeanCategories() {
    return Arrays.stream(FeeType.values())
            .map(EnumMapperValue::new)
            .collect(Collectors.toList());
}
```
![Alt text](/study/img/image-1.png)

Enum을 중심으로 View Layer과 Application, DB가 하나로 관리되도록 변경하였습니다. <br>
다만, 필요할 때마다 Enum, values를 통해 **Value 인스턴스를 생성하는 과정이 반복되는 것**이 아쉬운 점으로 남았습니다. <br>
런타임에 Enum 상수들이 변경될 일이 없기에, 관리 대상인 Enum들은 미리 Bean에 등록하여 사용하도록 변경해보았습니다. <br>
```java
public class EnumMapper {
    private Map<String, List<EnumMapperValue>> factory = new LinkedHashMap<>(); // Enum Value들을 담을 팩토리 클래스

    public EnumMapper(){}

    public void put(String key, Class<? extends EnumMapperType> e){
        factory.put(key, toEnumValues(e));
    }

    private List<EnumMapperValue> toEnumValues (Class<? extends EnumMapperType> e) { // EnumMapperType 인터페이스 구현체만 오도록 제한
        return Arrays.stream(e.getEnumConstants())
                .map(EnumMapperValue::new)
                .collect(Collectors.toList());
    }
    
    public List<EnumMapperValue> get(String key){
        return factory.get(key);
    }

    public Map<String, List<EnumMapperValue>> get(List<String> keys){
        if(keys == null || keys.size() == 0) {
            return new LinkedHashMap<>();
        }

        return keys.stream()
                .collect(Collectors.toMap(Function.identity(), key -> factory.get(key)));
    }

    public Map<String, List<EnumMapperValue>> getAll() { return factory; }
}
```
이를 Bean으로 등록하였습니다.
```java
@Bean
public EnumMapper enumMapper() {
    EnumMapper enumMapper = new EnumMapper();
    enumMapper.put("FeeType", FeeType.class); // 팩토리에 등록 후 원하는 곳에서 get 합니다.

    return enumMapper;
}

@GetMapping("/categories")
public List<EnumMapperValue> getCategories() { return enumMapper.get("FeeType"); }
```

## 마무리 
Enum을 적극적으로 활용하면 얻을 수 있는 장점을 알게 되었습니다.
<br><br>
도대체 이 코드가 어디에서 쓰이는지, 이 필드에는 어떤 값들만 허용이 가능한지, <br>
A값과 B값이 실제로는 동일한 것인지 전혀 다른 의미인지, <br>
이 코드를 사용하기 위해 추가로 필요한 메소드는 무엇이고, 변경되면 어디까지 변경해야 하는 것인지 등등 <br>
Enum을 통해 확실한 부분과 불확실한 부분을 분리할 수 있었습니다. <br><br>

특히 가장 실감했던 장점은 문맥(Context)을 담는다는 것입니다.<br>
A라는 상황에서 "a"와 B라는 상황에서 "a"는 똑같은 문자열 "a"지만 전혀 다른 의미입니다. <br>
문자열은 이를 표현할 수 없지만, Enum은 이를 표현할 수 있습니다.<br>
이로 인해 실행되는 코드를 이해하기 위해 **추가로 무엇을 찾아보는 행위를** 최소화 할 수 있게 되었습니다. <br>
이 코드의 의미와 용도를 파악하기 위해 엑셀과 워드를 찾고, 레거시 테이블을 Join & Group by 하고, PHP 코드를 다시 찾는 과정이 정말 비효율적이었습니다. <br><br>

**`Enum`을 사용하는데 있어 가장 큰 허들은 `변경이 어렵다`는 점입니다.** <br>
코드를 추가하거나 변경해야 하는 일이 빈번하다면, 매번 Enum 코드를 변경하고 배포하는 것보다 관리자 페이지에서 관리자가 직접 변경하는 것이 훨씬 편리할 수 있다고 생각합니다.<br><br>

하지만 우리가 관리하는 이 코드 테이블은 과연 얼마나 자주 변경되나요?<br>

한번 생성된 코드들은 얼마나 많은 테이블에서 사용되시나요?<br>
사용되는 테이블이 많아 변경하게 되면 관련된 테이블 데이터를 전부다 변경해야 하진 않으신가요?<br>
한번 생성된 코드테이블의 코드들을 변경할 일이 자주 있으셨나요?<br>
추가되는 코드는 한달에 몇번이나 발생하시나요?<br>
1년에 몇번 발생하시나요?<br>
매일 발생하시나요?<br>
하루에 배포는 몇번을 하시나요?<br>
저희팀을 예로 들면 하루에도 4~5번은 배포하고 있습니다.<br><br>

만약 위와 같은 상황이라면 테이블로 관리함으로써 얻는 장점이 정적언어를 활용함으로써 얻는 장점을 버릴정도로 더 큰지 고민해봐야할 문제라고 생각합니다.<br>
실제로 신규 정산 플랫폼은 단순 코드 테이블이 하나도 없습니다.<br>
구 정산 플랫폼은 3개의 코드테이블에서 수백개의 코드를 관리하고 있었지만, 이를 모두 제거하였습니다.<br>
그만큼 Enum을 적극적으로 사용하고 있고 그 효과를 보고 있습니다.<br>
(물론 망치질밖에 모르는 사람은 모든 것이 못으로 보인다는 말처럼 Enum으로 모든걸 해결하려고 하면 안된다고 생각합니다.<br>
적정선에서 Enum으로 관리할 부분과 테이블로 관리할 부분을 잘 나누어야 된다고 생각합니다.)<br><br>

해당 내용은 [우아한 기술블로그 백엔드 개발자 이동욱 님의 'Java Enum 활용기' 글입니다](https://techblog.woowahan.com/2527/)