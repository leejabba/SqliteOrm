# ORMLite 라이브러리 사용
## 1. ORMlite compile

**[build.gradle (Module:app)]**
```java
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    testCompile 'junit:junit:4.12'

    // 컴파일 한 후 싱크한다.
    // compile 'Group:Artifact:version'
    compile 'com.j256.ormlite:ormlite-core:5.0'
    compile 'com.j256.ormlite:ormlite-android:5.0'
}
```
[소스] (https://mvnrepository.com/artifact/com.j256.ormlite/ormlite-android/4.45)

* * *

## 2. Helper 클래스 설정

**[DBHelper.java]**
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

* * *

## 3. 테이블로 만들 수 있는 클래스 설정

**[Meno.java]**
```java
@DatabaseTable(tableName = "memo")  // 해당 클래스를 memo라는 이름의 테이블로 사용할 것임을 애너테이션으로 알려준다.
public class Memo {
    @DatabaseField(generatedId = true)  // 자동증가 옵션이라는 것을 애너테이션으로 알려준다.
    private int id;
    @DatabaseField  // 아래 속성을 필드로 사용할 것임을 애너테이션으로 알려준다.
    private String title;
    @DatabaseField
    private String content;
    @DatabaseField
    private Date date;

//    public Memo() {
//        // OrmLite는 생성자가 없으면 동작하지 않는다고 알려져있다.  이건 테스트 해볼 것.
//    }

    public Date getDate() {
        return date;
    }

    public void setDate(Date date) {
        this.date = date;
    }
```



