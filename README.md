# solvesql
#### 하루에 한 문제씩 꾸준히!
- 시작 : 2023년 11월 28일
- 종료 : ~


![스크린샷 2023-11-28 오전 9 12 57](https://github.com/juhoon212/solvesql/assets/121741140/8c627254-a9e3-46c5-96e3-8116695bbfcb)


------------------

### 모든 데이터 조회하기

```
select * from points
```

### 일부 데이터 조회하기

```
SELECT * 
FROM POINTS
WHERE QUARTET = 'I'
```

### 데이터 정렬하기
```
SELECT *
FROM points
WHERE quartet = 'I'
ORDER BY y
```

### 데이터 그룹으로 묶기

```
SELECT quartet,
        ROUND(AVG(x), 2) AS x_mean,
        ROUND(VARIANCE(x), 2) AS x_var,
        ROUND(AVG(Y), 2) AS y_mean,
        ROUND(VARIANCE(Y), 2) AS y_var
FROM points
GROUP BY quartet;
```

### 특정 컬럼만 조회하기
```
SELECT X,Y
FROM points
```

### 최근 올림픽이 개최된 도시
```
SELECT year, Upper(SUBSTRING(city,1,3)) as city
FROM GAMES
WHERE YEAR >= 2000
order by year desc
```

### 우리 플랫폼에 정착한 판매자 1
```
SELECT seller_id, COUNT(DISTINCT(ORDER_ID)) AS orders
FROM olist_order_items_dataset
GROUP BY seller_id
HAVING orders >= 100;
```

### 최고의 근무일을 찾아라
```
SELECT MAX(day) AS day, SUM(round(tip,2)) AS tip_daily
FROM tips
GROUP BY day
ORDER BY tip_daily DESC
limit 1
```

### 첫 주문과 마지막 주문
```
SELECT
  DATE(MAX(order_purchase_timestamp)) AS last_order_date,
  DATE(MIN(order_purchase_timestamp)) AS first_order_date
FROM
  olist_orders_dataset
```

### 많이 주문한 테이블 찾기

```
SELECT * 
FROM tips
WHERE total_bill > (
    SELECT AVG(total_bill)
    FROM tips
)
```

### 레스토랑의 일일 평균 매출액 계산하기

```
SELECT ROUND(AVG(PRICE), 2) AS avg_sales
from (
    SELECT SUM(total_bill) AS PRICE
    FROM tips
    GROUP BY day
) 
```

### 레스토랑의 영업일

`
SELECT DISTINCT(day) AS day_of_week
FROM tips
`

### 두 테이블 결합하기
```
SELECT DISTINCT(B.athlete_id)
FROM events A join records B 
              on A.id = B.event_id
WHERE A.sport = 'Golf'

```

### 레스토랑 웨이터의 팁 분석

```
SELECT  day,
        time,
        ROUND(AVG(tip), 2) AS avg_tip,
        ROUND(AVG(size), 2) AS avg_size
FROM tips
GROUP BY day, time
ORDER BY day, time
```

### 일별 블로그 방문자 수 집계

```
SELECT event_date_kst AS dt, count(DISTINCT(user_pseudo_id)) as users
FROM ga
WHERE int_value >= 1 
          AND event_date_kst BETWEEN '2021-08-02' AND '2021-08-09'
GROUP BY event_date_kst
ORDER BY dt
```

### 우리 플랫폼에 정착한 판매자 2
```
SELECT seller_id, count(DISTINCT(order_id)) as orders
FROM olist_order_items_dataset
where price >= 50
group by seller_id
having orders >= 100
order by orders desc
```

### 레스토랑의 일일 매출

```
SELECT day, SUM(total_bill) AS revenue_daily
FROM tips
GROUP BY day
HAVING revenue_daily > 1000
order by revenue_daily desc
```

### 버뮤다 삼각지대에 들어가버린 택배

```
SELECT date(order_delivered_carrier_date) AS delivered_carrier_date,
        count(*) as orders 
FROM olist_orders_dataset
WHERE order_delivered_customer_date is null AND
      date(order_delivered_carrier_date) BETWEEN '2017-01-01' AND '2017-01-31'
GROUP BY delivered_carrier_date
order by delivered_carrier_date
```

### 쇼핑몰 일일 매출액
```
SELECT date(B.order_purchase_timestamp) as dt,
       Round(sum(payment_value), 2) as revenue_daily
FROM olist_order_payments_dataset A JOIN olist_orders_dataset B
                              ON A.order_id = B.order_id
WHERE date(dt) >= '2018-01-01'
group by dt
order by dt
```

### 점검이 필요한 자전거

```
SELECT bike_id
FROM rental_history
WHERE DATE(rent_at) BETWEEN '2021-01-01' AND '2021-01-31'
GROUP BY bike_id
HAVING sum(distance) >= 50000
```

### 레스토랑의 대목

```
select *
FROM tips
where day in (
    SELECT day
    FROM tips
    group by day
    having sum(total_bill) >= 1500  
)
```

### 레스토랑의 요일별 VIP 

```
SELECT * 
FROM tips
GROUP BY day
HAVING MAX(total_bill)
```

### 작품이 없는 작가 찾기

```
    SELECT A.artist_id, A.name
    FROM artists A LEFT JOIN artworks_artists B 
                    ON A.artist_id = B.artist_id
    WHERE A.death_year <> ''
     AND B.artist_id IS NULL
```

### 멘토링 짝궁 테스트

```
SELECT e1.employee_id as mentee_id,
        e1.name as mentee_name,
        e2.employee_id as mentor_id,
        e2.name as mentor_name
FROM (
  SELECT *
  FROM employees
  WHERE STRFTIME('%Y%m%d',join_date) BETWEEN '20210901' AND '20211231'
) e1 JOIN employees e2
      ON e1.department != e2.department
WHERE STRFTIME('%Y%m%d',e2.join_date) <= '20191231'
order by mentee_id, mentor_id
```

### 쇼핑몰 일일 매출액과 ARPPU

```
SELECT  date(order_purchase_timestamp) as dt,
        count(DISTINCT(A.customer_id)) as pu,
        SUM(payment_value) as revenue_daily,
        ROUND(SUM(B.payment_value)/COUNT(distinct A.customer_id),2) AS arppu
FROM olist_orders_dataset A JOIN olist_order_payments_dataset B
                            ON A.order_id = B.order_id
WHERE dt >= '2018-01-01'
GROUP BY dt
ORDER BY dt
```
### 배송 예정일 예측 성공과 실패

```
SELECT date(order_purchase_timestamp) AS purchase_date,
        count(CASE
             WHEN order_delivered_customer_date < order_estimated_delivery_date
             THEN order_id
             END) AS 'success',
        count(CASE
             WHEN order_delivered_customer_date >= order_estimated_delivery_date
             THEN order_id
             END) AS 'fail'
FROM olist_orders_dataset
where date(order_purchase_timestamp) between '2017-01-01' AND '2017-01-31'
group by purchase_date
order by purchase_date

```


### 지역별 주문의 특징

```
SELECT A.Region,
      sum(CASE WHEN category = 'Furniture' THEN A.orderId END) as Furniture,
      sum(CASE WHEN category = 'Office Supplies' THEN A.orderId END) as 'Office Supplies',
      sum(CASE WHEN category = 'Technology' THEN A.orderId END) as Technology
FROM (
  SELECT region as 'Region',
          category,
          count(DISTINCT(order_id)) as orderId
  FROM records
  GROUP BY region, category
) as A
group by A.Region
order by A.Region
```

### 할부는 몇 개월로 해드릴까요

```
SELECT  payment_installments,
        count(DISTINCT(order_id)) as order_count,
        min(payment_value) as min_value, 
        max(payment_value) as max_value,
        avg(payment_value) as avg_value
FROM    olist_order_payments_dataset
WHERE payment_type = 'credit_card'
group by payment_installments
```
