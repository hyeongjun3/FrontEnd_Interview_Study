# IndexedDB

# IndexedDB

IndexedDB는 web storage 종류 중에 하나 입니다. 

대부분의 web storage는 작은 데이터를 저장하는데 효율적인 반면에, indexedDB는 blob이나 파일을 포함하는 구조화된 큰 데이터를 저장할 때 효과적인 storage입니다. 

API 이름처럼 IndexedDB는 SQL base의 RDBMS같이transaction을 사용하는 데이터베이스 시스템 입니다. 하지만, 고정된 column 테이블을 사용하는 SQL-base RDBMS와는 다르게 IndexedDB는 **JavaScript 베이스의 object-oriented 데이터베이스** 입니다. 즉, 구조화된 JavaScript object를 저장하는데 최적화 되어있습니다.

IndexedDB의  특징은 아래와 같습니다. 

## Key characteristics

indexedDB는 유저의 브라우저에 데이터를 영구적으로 저장하는 방법입니다. 네트워크 가능 여부와 상관없이 웹 애플리케이션이 비싼 쿼리를 할 수 있게 하기 때문에 애플리케이션이 online, offlined 모두에서 동작할 수 있게  해줍니다. (예: 메일)

IndexedDB는 `key` 를 이용하여  indexed된 객체를 저장하고 탐색할 수 있습니다. 또한 database에서 일어나는 모든 변경은 transaction과 같이 일어난다. 대부분의 웹 storage solution과 마찬가지로 indexDB는 `same-origin policy` 를 따른다. 그래서 너는 domain에서 저장된 데이터를 접근할 수 있지만, 다른 도메인에서 접근할 수 없다.

다른 타입의 database처럼 사용하면 당황할 수 있습니다. IndexedDB는 아래와 같은 특징을 가지고 있습니다.

### IndexedDB database store key-value pairs

values는 복잡하고 구조화된 object가 될 수 있습니다. 그리고 key는 이 객체의 properties입니다. Object를 빠르기 찾기 위한 index를 만들 수 있습니다. 

### IndexedDB is build on a transactional database model

indexedDB에서 일어나는 모든 변경은 transaction 문맥 안에서 발생합니다.

indexedDB API는 indexex, tabels, cursores 등 많은 객체를 제공합니다. 그리고 이 객체들은 전부 특정한 transaction과 묶여있습니다. 그러므로 transaction 밖에서는 어떠한 것도 할 수 없습니다. 

transaction은 lifetime을 가지고 있기 때문에 만료 이후에  transaction을 시도하면 exception이 throw 됩니다.

transaction은 auto-commit되고 manually하게 commit 할 수 없습니다.

IndexedDB의 transaction 모델은 유저가 웹 앱의 2개의 인스턴스를 동시에 실행할 때 매우 유용합니다. (2개의 탭을 사용할 때) 

### The IndexedDB API is mostly asynchronous

IndexedDB API는 대부분 비동기 API이기 떄문에 callback 함수를 전달해야 합니다. 풀어서 이야기하면, 동기적으로 database에 값을 저장하거나 탐색할 수 없습니다. 대신에 database에 요청한 request 객체를 전달 받을 수 있습니다. 요청한 operation이 끝나면 request 객체의 DOM event를 통해서 콜백을 실행할 수 있습니다. 길게 썼지만 AJAX와 똑같습니다.

Indexed DB API 요청의 결과로 request 객체를 전달받고 성공이나 실패를 DOM event를 통해서 받을 수 있습니다. request는 `readyState` `result` `errorCode` 속성 등을 통해서 상태를 알 수 있습니다.

### IndexedDB is object-oritented

IndexedDB는 SQL-based DB와 다르게 고정된 row와 column의 collection을 표현하는 relational database가 아닙니다. row, column data 테이블을 저장하는 전통적인 relational data store와 다르기 때문에 디자인도 달라질 것 입니다. IndexedDB는 data type을 저장할 수 있는 object store를 저장해서 JavaScript object를 저장할 수 있습니다. 편하긴 하지만 query와 iterate 성능을 최적화를 위해서는 많은 노력이 필요합니다.

### IndexedDB does not use Structured Query Language (SQL)

NoSQL 처럼, indexedDB는 SQL 대신 cursor를 이용하여 query를 합니다. 

### IndexedDB adheres to a same-origin policy

indexedDB는 same-origin policy를 가지고 있습니다. 도메인이 달라지면 database도 변경 됩니다.

## Limitations

IndexedDB는 client-side storage의 필요성으로 인해서  만들어 졌기 때문에 몇몇 상황에 대해서는 고려 되어있지 않습니다.

### Internationalized sorting

모든 언어가 string을  같은 방법으로 정렬하지 않기 때문에 internationalized 소팅을 지원하지 않습니다. 즉, 개발자가 효율적으로 database를 정렬해야합니다. 하지만 Firefox는 지원해줍니다?

### Synchronizing

API는 서버에 있는 database와 synchronizing하게 디자인 되어 있지 않습니다. 즉 서버 DB의 레코드를 저장하기  위해서는 개발자의 손길이 필욯다ㅏ.

## Full text searching

`LIKE` 같은 동작이 없습니다.

### 데이터가 지워지는 경우

- 유저가 브라우저의 데이터를 비우는
- 대부분의 브라우저에서는 session이 끝나면 web storage를 비웁니다.
- 디스크나 쿼터 제한에 도달하면 지워집니다.
- 데이터가 부서지거나 수정될 떄 지워집니다.