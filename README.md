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

### 데이터 그룹으로 묶기

`
SELECT quartet,
        ROUND(AVG(x), 2) AS x_mean,
        ROUND(VARIANCE(x), 2) AS x_var,
        ROUND(AVG(Y), 2) AS y_mean,
        ROUND(VARIANCE(Y), 2) AS y_var
FROM points
GROUP BY quartet;
`

### 특정 컬럼만 조회하기
`
SELECT X,Y
FROM points
`

### 최근 올림픽이 개최된 도시
`
SELECT year, Upper(SUBSTRING(city,1,3)) as city
FROM GAMES
WHERE YEAR >= 2000
order by year desc
`

### 우리 플랫폼에 정착한 판매자 1
`
SELECT seller_id, COUNT(DISTINCT(ORDER_ID)) AS orders
FROM olist_order_items_dataset
GROUP BY seller_id
HAVING orders >= 100;
`

### 최고의 근무일을 찾아라
`
SELECT MAX(day) AS day, SUM(round(tip,2)) AS tip_daily
FROM tips
GROUP BY day
ORDER BY tip_daily DESC
limit 1
`

### 첫 주문과 마지막 주문
`
SELECT
  DATE(MAX(order_purchase_timestamp)) AS last_order_date,
  DATE(MIN(order_purchase_timestamp)) AS first_order_date
FROM
  olist_orders_dataset
`
