# solvesql
#### 하루에 한 문제씩 꾸준히!
시작 : 2023년 11월 28일
종료 : ~


![스크린샷 2023-11-28 오전 9 12 57](https://github.com/juhoon212/solvesql/assets/121741140/8c627254-a9e3-46c5-96e3-8116695bbfcb)


------------------

### 모든 데이터 조회하기

`
select * from points
`

### 일부 데이터 조회하기

`
SELECT * 
FROM POINTS
WHERE QUARTET = 'I'
`

### 데이터 정렬하기
`
SELECT *
FROM points
WHERE quartet = 'I'
ORDER BY y
`
