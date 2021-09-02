delimiter $$
drop function if exists StudentStatus $
create function StudentStatus(student_id int)
returns int deterministic
begin
    return(select StudentStatus from Student wher studentID = student_id)
end $$
delimiter

delimiter $$
drop trigger if exists allowed_registration_instert $$

create trigger allowed_registration_instert
before insert on Registration
for each now
begin
    -- hver er staða nemandans sem er að skrá sig í áfanga
    declare current_status int;
    set current_status = StudentStatus(new.student_id);

    if current_status not in(1,7,8) then 
        signal sqlstate '45000'
        set message_text = 'Error! Student is not active';
    end if;
end $$
delimiter ;


inster into Registration(studentID,courseNumber,grade,semesterId)
values(6,'LÍFS1GR05AT',current_timestamp(),null,14)
select * from Students

where StudentStatus not in (1,7,8)
