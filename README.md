# Code to solve problem about sql in hackerRank 15 days of Learning SQL
declare @hacker_sub_eachday table
(
    hacker_id int,
    submission_date date
)
insert into @hacker_sub_eachday
select hacker_id, submission_date
from submissions
where submission_date = '2016-03-01'

declare @date_ date = '2016-03-01'
declare @date_2 date = '2016-03-01'

while (@date_ < '2016-03-15')
begin
    set @date_ = dateadd(dd,1,@date_)
    insert into @hacker_sub_eachday
    select s.hacker_id, s.submission_date
    from submissions as s
    inner join @hacker_sub_eachday as s1 on s.hacker_id = s1.hacker_id and s1.submission_date like @date_2
    where s.submission_date like @date_
    set @date_2 = dateadd(dd,1,@date_2)
end
;with count_hacker_sub_eachday as
(select s.submission_date, count(distinct s.hacker_id) as quantity
from @hacker_sub_eachday as s
group by s.submission_date)

,max_hacker_id as 
(select row_number() over(partition by s.submission_date order by count(s.hacker_id) desc, s.hacker_id asc) as rowNumber, s.submission_date, s.hacker_id, h.name
from submissions as s
inner join hackers as h on h.hacker_id = s.hacker_id
group by s.submission_date, s.hacker_id,h.name)

select mh.submission_date, ch.quantity, mh.hacker_id, mh.name
from max_hacker_id as mh
inner join count_hacker_sub_eachday as ch on ch.submission_date = mh.submission_date
where mh.rowNumber = 1
