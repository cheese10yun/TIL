# DataGrip

DataGrip은 JetBrains에서 만든 데이터베이스 및 SQL 용 크로스 플랫폼 IDE입니다. 자세한 소개는 [DataGrip](https://www.jetbrains.com/ko-kr/datagrip/)에서 확인 가능합니다.

다양한 제품들이 있지만 저는 DataGrip 선호하며 애용하고 있는 제품입니다. 공식 자료에서도 충분히 다양한 기능들을 소개해 주고 있지만 개인적으로 DataGrip에 좋은 기능들을 정리해 보겠습니다.

## 다양한 플랫폼 지원

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-1.png)

DataGrip은 다양한 플랫폼을 지원하기 때문에 한 가지 도구를 이용하여 여러 플랫폼에 대한 제어가 가능합니다. 동일한 도구를 사용하기 때문에 단축키 및 플러그인 등을 그대로 사용이 가능하여 좋은 생산성을 제공해 줍니다.


## 조회한 데이터에 대한 가공

조회한 데이터를 다양한 형식으로 가공 처리를 도와줍니다. 데이터를 조회한 이후에 추출할 형식을 지정하고 원하는 데이터를 선택하고 복사하기만 하면 됩니다.

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-3.png)

```csv
id,amount,created_at,order_id,updated_at
1,10.00,2022-09-23 17:52:00,1,2022-09-23 17:52:00
2,10.00,2022-09-23 17:52:00,1,2022-09-23 17:52:00
3,10.00,2022-09-23 17:52:02,1,2022-09-23 17:52:02
4,10.00,2022-09-23 17:52:03,1,2022-09-23 17:52:03
5,10.00,2022-09-23 17:52:03,1,2022-09-23 17:52:03
```

CSV 형식으로 복사를 하면 위 형식처럼 복사가 됩니다.

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-4.png)

CSV 형식 같은 경우는 다양한 형식으로 변경이 가능합니다.

SQL-insert-Multirow 형식도 매우 유용합니다. 조회한 결과에 한 insert query를 복사할 수 있습니다.

```sql
insert into study.payment (id, amount, created_at, order_id, updated_at)
values  (1, 10.00, '2022-09-23 17:52:00', 1, '2022-09-23 17:52:00'),
        (2, 10.00, '2022-09-23 17:52:00', 1, '2022-09-23 17:52:00'),
        (3, 10.00, '2022-09-23 17:52:02', 1, '2022-09-23 17:52:02'),
        (4, 10.00, '2022-09-23 17:52:03', 1, '2022-09-23 17:52:03'),
        (5, 10.00, '2022-09-23 17:52:03', 1, '2022-09-23 17:52:03');
```

## 데이터 추출

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-5.png)

조회한 데이터를 추출하는 경우 다양한 형식의 데이터 추출을 지원합니다. 


## 자동 완성 기능

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-6.png)

강력하게 자동 완성을 제공해 줍니다. join 쿼리 같은 경우에는 대상 테이블끼리의 칼럼을 비교해서 조인 대상이 될만한 칼럼을 추천해 줍니다.

## ERD 지원

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-7.png)

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-8.png)

테이블 간의 관계를 표현해 주는 ERD 기능을 제공합니다. **물리적인 FK를 맺지 않아도 테이블과 칼럼을 분석해서 연결해 줍니다.** 실제 프로덕트 환경에서는 다양한 이유로 물리적인 FK를 맺지 않고 사용해서 이 기능이 더 큰 장점인 거 같습니다.


## Visual Explain 지원

`explain`를 시각화하여 제공합니다. 디테일한 내용을 확인하는 것보다는 부족하지만 간단한 쿼리 계획이나 해당 쿼리를 개선하여 리포팅할 매우 유용합니다.

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-9.png)

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-10.png)

## SQL Scripts 기능

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-11.png)

```sql
create table study.orders
(
    id           bigint auto_increment
        primary key,
    order_number decimal(19, 2) not null,
    created_at   datetime       not null,
    order_id     bigint         not null,
    updated_at   datetime       not null
);

```

`SQL Scripts` 기능으로 도움 되는 기능들이 있습니다. 저 같은 경우에는 Generate DDL to Clibboard 기능을 자주 사용합니다. sandbox 환경에서 설계 및 개발을 진행하면서 테이블에 대한 변경이 이루어지기 때문에 처음에 작성한 DDL로 운영에 반영되는 일은 흔하지 않은 거 같습니다. 이렇게 설계가 다 끝난 시점에서 해당 DDL을 추출하여 운영에 반영합니다.


## 다양하고 편리한 플러그인 지원

Jetbrains에서 제공하고 있는 다양한 플러그인이 존재하며 다른 Jetbrains의 제품에서 사용하던 플러그인을 그대로 사용할 수 있습니다. 아래 소개한 플러그인 외에도 유용한 플러그인이 많기 때문에 플러그인 탭에서 다운로드 높은 순으로 정렬하여 좋은 플러그인을 찾아보는 것을 권장 드립니다.

### Settings Repositroy

Settings Repositroy는 해당 툴에 대한 단축키를 비롯한 다양한 설정을 Github에 저장하여 여러 환경에서 동일한 환경을 설정할 수 있게 해줍니다. 해당 설정은 XML으로 관리하며 변경 분을 Pull & Push 하여 설정을 동기화합니다. 저 같은 경우에는 단축키 및 다양한 설정들을 많이 하여 매우 유용하게 사용하고 있습니다.

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-12.png)

![](https://raw.githubusercontent.com/cheese10yun/TIL/master/assets/DataGrip-13.png)

해당 플러그인을 설치하고 `File -> Manage IDE Settings -> Settings Repositroy...`에서 본인의 깃헙 주소로 설정하면 됩니다.

### String Manipulation

## Increment 기능

![](https://raw.githubusercontent.com/cheese10yun/IntelliJ/master/assets/string-manipulation-1.gif)

특정 값에 대해서 자동으로 증가시켜 중복되지 않는 값으로 설정할 수 있습니다.

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/kotlin-jpa/docs/string-maniplation-2.png)

위 이미지와 같은 FK 참조하는 값에 대한 SQL을 작성하는 경우 매우 효율 적입니다. 이전에 [Sql을 통해서 테스트 코드를 쉽게 작성하자](https://cheese10yun.github.io/sql-test/) 포스팅도 참고하시면 좋을 거 같습니다.

## Switch

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/kotlin-jpa/docs/string-manipulation-3.gif)

Switch는 다양한 문자열 포맷으로 쉽게 변경이 가능합니다.

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/kotlin-jpa/docs/string-manipulation-4.png)

Switch Case 항목을 보시면 매우 다양한 항목으로 변경이 가능합니다.