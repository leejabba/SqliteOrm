# ORMLite 라이브러리 사용시 Helper 생성

```java
public class DBHelper extends OrmLiteSqliteOpenHelper {
    public static final String DATABASE_NAME = "database.db";
    public static final int DATABASE_VERSION = 1;

    // 최초 호출될 때 database.db 파일을 /data/data/패키지명/database/ 디렉토리 아래에 생성해준다.
    public DBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    // 최초에 생성되면 버전 체크를 해서 onCreate 메서드를 호출한다.
    @Override
    public void onCreate(SQLiteDatabase database, ConnectionSource connectionSource) {
        // 테이블을 생성해야 한다. 테이블 생성을 아주 쉽게 만들어주는게 ORMlite의 장점이다.
        try {
            TableUtils.createTable(connectionSource, Memo.class);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // DATABASE VERSION이 업그레이드 되면 호출된다. 단, 최초로 앱을 설치하는 사람은 onCreate가 호출된다.
    // 기존 앱 설치자는 onCreate를 건너뛰고 onUpgrad가 호출된다.
    @Override
    public void onUpgrade(SQLiteDatabase database, ConnectionSource connectionSource, int oldVersion, int newVersion) {
        /* Memo 테이블의 특정 컬럼만 변경 또는 추가 되어서 VERSION이 변경되었을 경우 (단, 애너테이션이 붙지 않은 속성 변경은 상관이 없다.)
         0. VERSION 넘버를 변경한다.
         1. 기존 테이블을 백업하기 위한 임시테이블을 생성하고 데이터를 담아둔다.
            예) create table temp_memo;     <- 기존 데이터
         2. Memo 테이블을 삭제하고 다시 생성한다.
         3. 백업된 데이터를 다시 입력한다.
         4. 임시 테이블 삭제
        */
    }
}
```
