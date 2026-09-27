--This project contians  of three Tabels related to apartment rentals

select * from Locations
select * from PropertyDetails
select * from Apartments

--Converted selected state abbreviations to full state names  , keeping the remaining abbreviations unchanged

select *,case when state= 'GA' then 'Georgia'
		when state= 'Fl' then 'Florida'
		when state= 'CA' then 'California' else state end as 'fullname_state'
from Locations

--display all  ID and Amenities that have gym and elevator in department

select pd.id, pd.amenities from PropertyDetails pd
where amenities like '%gym%' and amenities like '%elevator%'

--display all body Apartemtns in Florida  and have more than 3 bedrooms and their prices

select a.body , a.price
from Apartments a inner join Locations l on a.id=l.id inner join PropertyDetails pd on a.id=pd.id
where l.state='FL' and pd.bedrooms>=3

--fetch the number of apartments and its total prices of each state

select  l.state , count(a.id) as "total apartments in each state" , sum(a.price) as "total prices"
from Apartments a inner join Locations l on a.id=l.id
group by l.state

--find the highest price in each city

select l.cityname , max(a.price)
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
group by l.cityname
 
 --find top 3 most expensive states

 select top 3 l.state , sum(a.price)
 from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
 group by l.state
 order by sum(a.price) desc

 --find the rank prices for each state

select l.state ,a.price , rank() over (partition by l.state order by a.price desc) as 'rank for each dept'
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id

-- find cityname with a price above the overall average apartment price 

select l.cityname,a.price
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
where a.price >(select avg(price) from Apartments)

--compare the average price of pet-friendly and non pet-friendly apartments

select pd.pets_allowed ,avg(a.price) as 'avg prices' 
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
group by pd.pets_allowed

--rank apartments by price within each state and calculate their price percentage of the state goal

select a.id,l.state ,a.price ,rank() over (partition by l.state order by a.price ) , a.price*100.00/sum(a.price) over(partition by l.state)
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id

--calculate the running total of apartment prices within each state

select l.state , sum(a.price) over(partition by l.state order by a.price desc) as "running total"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id

--compare each apartments price with their previous apartment in the same city

select l.cityname , lag(a.price) over(partition by l.cityname order by a.price desc )
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id

--compare each apartments price with their next apartment in the same city

select l.cityname , lead(a.price) over(partition by l.cityname order by a.price desc )
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id

-- find apartments whose price is above their state average 
--we need use CTE
with ap as(
select a.id , l.state, a.price , avg(a.price) over (partition by l.state) as "avgpricestate"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
)

select * from ap
where price > avgpricestate


--rank apartments by square feet within each state
select a.id,l.state,pd.square_feet ,rank() over(partition by l.state order by pd.square_feet) as 'ranking'
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id

-- find the largest apartment in each city
with s as (
select  a.id,l.cityname,row_number() over (partition by l.cityname order by pd.square_feet desc) as "ordering"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
)
select * from s
where "ordering" = 1

--calculate each apartments price as a percentege of its city total apartment prices
with appr as(
select a.id,l.cityname , a.price,sum(a.price) over (partition by l.cityname) as "pc"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
)
select *, price*100.00/pc from appr


--find the top 3  largest apartments in each state

with asss as(
select a.id, l.state, pd.square_feet, ROW_NUMBER() over(partition by l.state order by pd.square_feet desc) as "ordering"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
)
select * from asss
where "ordering" <=3

-- find the average apartment price by number of bedrooms for each state

select l.state, pd.bedrooms ,avg(a.price) as " avg price per bedroom" 
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
group by l.state , pd.bedrooms
order by l.state, pd.bedrooms

--find the percentege of apartments with each number of bedrooms in each state
with bedroomscount as (
select l.state , pd.bedrooms , count(a.id) as "apartmentcount"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
group by l.state , pd.bedrooms
)

select state,bedrooms,apartmentcount*100.00/ sum(apartmentcount) over(partition by state) 'percentege'
from bedroomscount

-- find the number of apartments for each number of bedrooms in each state and calculate the percentage of each category within its state

with  ca as(
select l.state , pd.bathrooms , count(l.state) as "number of apartments for each bathroom"
from PropertyDetails pd  inner join Locations l on pd.id=l.id  inner join Apartments a on pd.id=a.id
group by l.state , pd.bathrooms
)

select state , bathrooms , "number of apartments for each bathroom"*100.00/sum("number of apartments for each bathroom") over (partition by state)
from ca

-- view summarizing the number of apartments and average price for each state

create view apartment_summary as

select l.state ,count(a.id) as "apartmentcount", avg(a.price) "avgprice"
from Locations l inner join Apartments a on l.id=a.id
group by l.state

select * from apartment_summary
