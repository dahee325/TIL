# RDBMS(Relation DataBase Management System)
- 표 형식으로 저장, SQL문으로 작성
- SQL이라고 부름 => 그 외는 NoSQL이라고 부름(document(ex. Mongo DB), key-value, graph 등)

# MySQL 설치
- mysql 홈페이지 -> download -> 맨 아래 community ~ -> `Community Server`와 `MySQL Workbench`설치
- `Communiry Server` : 데이터베이스 자체
    1. 버전 변경 : 8.4.4 LTS(Long-Term-Service)
    2. NSI Installer
    3. 만약 Visual C++오류 생기면 슬랙 링크 접속 -> X64 설치
    4. accept 동의
    5. 첫번째(t어쩌구) 선택
    6. 설정변경 -> accounts and Roles : password설정(`password`로 함)
    7. Next -> Execute
    8. 설치 완료
- `MySQL Workbench` : 데이터 베이스에 접근 => 꼭 필요하지는 않고 대신 외부의 `DBeaver`와 같은 툴 사용 가능
    1. 별 다른 설정 필요 없음 -> Install
    2. 잘 설치됐으면 `Welcome to MySQL Workbench`창이 뜸
    3. Local instance MySQL 더블클릭
    4. password 입력
    5. Communty serer 들어가기

# MySQL
- `mydb` schema 생성
- `Query` 이름을 `0.DDL`로 설정

# SQLite
- vs code -> extensions -> `SQLite` 프로그램 설치
- `sqlite3/0.DDL.sql` 폴더와 파일 생성
- `sqlite3/db.sqlite3` 파일 생성


# 0. settings
- `python -m venv venv`
- `source venv/Scripts/activate`
- `pip install django faker`
- `.gitignore` 설정 : python , windows, macOS, django
- `pip freeze >> requirements.txt`


## 프로젝트/앱 생성
- `django-admin startproject sql_orm .`
- `django-admin startapp movies`
- `sql_orm/settings.py`에 `movies`앱 등록

# 1. modeling / migration
- `movies/models.py`에 슬랙 공유코드 복붙
- `python manage.py makemigrations`
- `python manage.py migrate`
- `python manage.py sqlmigrate movies 0001` : ORM(Object Relation Mapping)이 우리가 입력한 명령어를 SQL로 만들어준 내용 확인..?

# 2. DATABASES 변경
- `sqlite3`에서 `MySQL`로
- `sql_orm/settings.py`에서 `DATABASES`설정 변경
```python
# 기존에 있던 DATABASES는 주석처리하고 새로 작성

DATABASES = {
    'default': {
        'ENGINE' : 'django.db.backends.mysql',
        'NAME': 'mydb', # workbench에서 만든 mydb
        'HOST' : 'localhost', # mysql 서버 주소
        'PORT' : '3306',
        'USER' : 'root', # mysql 서버에 누가 접속했는지
        'PASSWORD' : 'password',
    }
}
```

- `python manage.py migrate` : 데이터베이스 공간을 바꿨기 때문에 다시 알려줘야함 => `mysqlclient`설치하라고 오류
- `pip install mysqlclient`
- `python manage.py migrate` => 잘 됨, Workbench에 들어가서 Tables이 생겼는지 확인

- 그치만 우리는 sqlite3로 할 것 => workbench가 편하지 않음 => 새로 만든 DATABASES 주석처리하고 원래 코드 사용

# 3. generate
- `moveis`폴더 안에 `management`폴더 생성
- `movies/management`폴더 안에 `commands`폴더와 `__init__.py`파일 생성
- `movies/management/commands`폴더 안에 `__init__.py`와 `generate.py` 파일 생성
- `movies/management/commands/generate.py`에 슬랙에 공유된 코드 복붙
- `python manage.py generate` : db에 `generate.py`에 있는 데이터 저장하는 코드 => 코드를 2번 실행하면 2번 쌓임


# 4. DataBase 조작
- `pip install django-extensions` 설치 -> shell기능 별로여서 `ipython`다시 설치
- `sql_orm/settings.py`에 `django-extensions` 앱 등록
- `python manage.py shell_plus` : ORM 사용
- `pip install ipython` 설치 -> `python manage.py shell_plus`다시 실행 -> `orm`실행 -> `exit()`입력해서 나가기

## 4-1. CREATE
- `sqlite3/ 1.insert.sql`파일 생성 : 테이블에 내가 가진 데이터 삽입
```sql
-- orm 1 => `클래스이름.objects.create(추가하고싶은변수이름=값)`
-- 영화이름은 'dark night'이고 개봉한 년도는 2008년인 영화 추가 
Movie.objects.create(
    title='dark night',
    year=2008
)

-- orm 2\
-- => 클래스 이름 설정 = 클래스이름()
-- => 설정한 클래스 이름.변수이름 = 추가할 값\
-- => 설정한 클래스 이름.save()
-- 영화이름은 'spider man'이고 개봉한 년도는 2010년인 영화 추가가
movie = Movie()
movie.title = 'spider man'
movie.year = 2010
movie.save()

-- sql => `INSERT INTO 테이블 이름 (만들고싶은 변수 이름) VALUES (값)`
-- 영화이름은 'dark night'이고 개봉한 년도는 2008년인 영화 추가 
INSERT INTO movies_movie (title, year)
VALUES ('dark night', 2008);
```


## 4-2. READ
- `sqlite3/2.select.sql`파일 생성
- 테이블에서 전제 데이터 가져오기
```sql
-- movie데이터 가져오기


-- orm => `클래스이름.objects.all()`
Movie.objects.all()

-- sql => `SELECT * FROM 테이블 이름`
SELECT * FROM movies_movie;
```

- 특정 변수를 기준으로 정렬해서 전체데이터 가져오기
```sql
-- 년도순으로 정렬해서 movie데이터 가져오기


-- orm => `클래스이름.objects.all().order_by('정렬할 변수이름')`
Moive.objects.all().order_by('year')

-- sql => `SELECT * FROM 테이블 이름 ORDER BY 정렬할 변수 이름`
SELECT * FROM movies_movie
ORDER BY year DESC; -- 역순 정렬
```

- 조건에 맞는 데이터 골라내기
- orm => `클래스이름.objects.filter(조건)`
- sql => `SELECT * FROM 테이블 이름 WHERE 조건`
- django-lookup-filter

1. 나이가 31인 사람만 출력
```sql
-- orm
User.objects.filter(age=31)

-- sql
SELECT * FROM movies_user
WHERE age=31
```

2. 20살 미만인 사람만 출력
```sql
-- orm
User.objects.filter(age__lt=20) -- lt(less then)

-- sql
SELECT * FROM movies_user
WHERE age<20;
```

3. 20대인 사람만 출력
```sql
-- orm
User.objects.filter(age__ate=20, age__lt=30) -- gte : grater then equal

-- sql
SELECT * FROM movies_user
WHERE age >= 20 and age < 30;
```

4. 2000년도 이전과 2010년 이후 영화 출력
```sql
-- orm
Movie.objects.filter(Q(year__lt=2000) | Q(year_gt=2010))
-- Q : 쿼리, or이나 not을 사용할 때 사용

-- sql
SELECT * FROM movies_movie
WHERE year < 2000 or year > 2010;
```

5. 가장 최근에 개봉한 영화 출력
```sql
-- orm
Movie.objects.aggregate(Max('year'))

-- sql
SELECT MAX(year) FROM movies_movie;
```

6. 최신 영화의 제목과 년도 출력
```sql
-- orm
Movie.objects.values('title', 'year').order_by('-year')[0]
-- orm에서 aggregate는 계산이 목적이므로 값만 출력 => order_by사용

-- sql
SELECT title, MAX(year) FROM movies_movie;
```

7. 사용자의 평균나이 출력
```sql
-- orm
User.objects.aggregate(Avg('age'))

-- sql
SELECT AVG(age) FROM movies_user;
```

8. 1번 영화의 평균 평점
```sql
-- orm
Score.objects.filter(id=1).aggregate(Avg('value'))

-- sql
SELECT AVG(value) FROM movies_score
WHERE movie_id=1;
```

9. 1번 사용자가 작성한 평점의 개수
```sql
-- orm 1
len(Score.objects.filter(user_id=1))
-- orm 2
Socre.objects.filter(user_id=1).count()

-- sql
SELECT COUNT(*) FROM movies_score
WHERE user_id=1;
```


## 4-3. UPDATE
- `sqlite3/3.update.sql`파일 생성
- 데이터 수정하기
```sql
-- 1번 영화 'spider man'/'iron man'으로 수정


-- orm => 데이터 생성과 비슷
movie = Movie.objects.get(id=1) -- 데이터 선택
movie.title = 'spider man'
movie.save() -- 저장까지 해야 db가 바뀜

-- sql => `UPDATE 테이블 이름 SET 수정할 변수이름=수정할 변수 값 WHERE 조건`
UPDATE movies_movie
SET title='iron man'
WHERE id=1;
```


## 4-4. DELETE
- `sqlite3/4.delete.sql`파일 생성
- 데이터 삭제
```sql
-- 1번/2번 영화 삭제


-- orm
movie = Movie.objects.get(id=1)
movie.delete() -- delete는 저장안해도됨

--sql => `DELETE FROM 테이블 이름 WHERE 조건`
DELETE FROM movies_movie
WHERE id=2;
```


## 4-5. JOIN(select문의 연장선)
- `sqlite3/5.join.sql`파일 생성

1. 1번 사용자가 작성한 모든 평점 출력하기
```sql
-- orm
User.objects.get(id=1).score_set.all() -- User클래스 안에는 score_set이라는 변수가 숨겨져 있음

-- sql
-- movies_user, movies_score 2개의 테이블 합치기
SELECT * 
FROM movies_user JOIN movies_score
ON movies_user.id = movies_score.user_id -- 기준이 되는 변수 알려주기
WHERE movies_user.id=1; -- 조건 설정, id가 2개기 때문에 어느 테이블의 id인지 알려줘야함
-- 변수 이름을 바꿔서 출력하고싶으면 AS사용 => `SELECT name, movies.score.id AS score_id`
```

2. 100번영화가 어떤 카테고리에 속해있는지 출력하기
```sql
-- orm
Movie.objects.get(id=100).categories.all()

-- movies_movie와 movies_category 사이에는 movies_category_movies라는 중간 테이블이 존재
-- movies_move, movies_category_movies, movies_category 3개의 테이블 합치기
-- sql
SELECT *
-- 중간 테이블을 중간에 사용
FROM movies_moive JOIN movies_category_movies
ON movies_movie.id = movies_category_movies.movie_id
-- 2개의 테이블을 합친 후 나머지 하나 합침
JOIN movies_category
ON movies_category_movies.category_id = movies_category.id
WHERE movies_movie.id=100;
```

3. 카테고리가 드라마인 모든 영화 출력
```sql
-- orm
Category.objects.get(name'drama').movies_all()

--sql
-- movies_move, movies_category_movies, movies_category 3개의 테이블 합치기
SELECT *
FROM movies_category JOIN movies_category_movies
ON movies_category.id = movies_category_movies.category_id
JOIN movies_movie
ON movies_category_movies.movie_id = movies_movie.id
WHERE movies_category.name='drama';
```

- `group by`
1. 사용자의 나라와 나라에 사는 사람의 수
```sql
-- orm
-- annotate() : 새로운 컬럼을 하나 더 추가
User.objects.values('country').annotate(Count('id'))

-- sql
SELECT country, COUNT(*) FROM movies_user
GROUP BY country;
```

2. 나라별 평균 점수
```sql
-- orm
-- score__value : User와 1:N의 관계인 Score 테이블의 value변수 가져오기
User.objects.values('country').annotate(Avg('score__value'))

-- sql
-- movies_user, movies_score 합치기
SELECT country, AVG(value)
FROM movies_user JOIN movies_score
ON movies_user.id = movies_score.user_id
GROUP BY country;
```