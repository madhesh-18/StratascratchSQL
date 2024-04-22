# StratascratchSQL
#SQL code, I have used while practicing in stratascratch website

#ID 514

select count(distinct user_id) from marketing_campaign 
where user_id in (
select user_id from marketing_campaign 
group by user_id
having count(distinct created_at)>1 and count(distinct product_id)>1)
and concat((user_id), '_', (product_id)) not in 

(select concat((user_id), '_', (product_id)) as user_product from 

(select *, rank() over(partition by user_id order by created_at) as rn from marketing_campaign
) temp
where rn =1)


#ID 2005

select temp2.open_users/temp1.all_users from 
(select count(distinct user_id) as all_users from fb_active_users where country = 'USA') as temp1, 
(select count(distinct user_id)  as open_users from fb_active_users where country = 'USA' and status = 'open') as temp2

#ID 2024 

select client_id, month(time_id) as month, count(distinct user_id) from fact_events
group by client_id, month

#ID 2029

with cte as
    (select 
        user_id, 
        100 * (sum(if(event_type in ('video call received', 'video call sent', 'voice call received', 'voice call sent'), 1, 0))/count(*)) as percentage
    from fact_events 
    group by 1
    having percentage >= 50),


cte2 as
    (select 
        client_id, 
        count(*) as cnt 
    from fact_events 
    where user_id in (select distinct user_id from cte) 
    group by 1)

select client_id from cte2 where cnt = (select max(cnt) from cte2)
