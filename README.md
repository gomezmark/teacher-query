# teacher-query

## Question 1
### Answer:

```
SELECT 
	T.id,
    T.nickname,
    CASE
		WHEN T.status = 1 THEN 'Active'
        WHEN T.status = 2 THEN 'Deactivated'
        ELSE 'Discontinued'
	END AS 'status',
    (SELECT 
		GROUP_CONCAT(
			CASE
				WHEN role = 1 THEN 'Trainer'
                WHEN role = 2 THEN 'Assessor'
                WHEN role = 3 THEN 'Staff'
			END
        ) 
		FROM trn_teacher_role R
        WHERE R.teacher_id = T.id
        ) as 'role'
        
FROM trn_teacher T;
```

## Question 2
### Answer
```
SELECT 
	T.id,
    T.nickname,
    (
		SELECT COUNT(status)
		FROM trn_time_table TT
        WHERE TT.teacher_id = T.id and TT.status = 1
	) as 'Open',
        (
		SELECT COUNT(status)
		FROM trn_time_table TT
        WHERE TT.teacher_id = T.id and TT.status = 2
	) as 'Reserved',
	(
		SELECT COUNT(TE.result)
		FROM trn_evaluation TE
        WHERE TE.teacher_id = T.id and TE.result = 1
	) as 'Taught',
    (
		SELECT COUNT(*)
		FROM trn_evaluation TE
        WHERE TE.teacher_id = T.id and TE.result = 2
	) as 'No Show'

FROM trn_teacher T
INNER JOIN trn_teacher_role R 
ON T.id = R.teacher_id
WHERE R.role IN (1, 2) AND T.status IN (1, 2)
GROUP By T.id;
```
