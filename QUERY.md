# Query con GROUP BY

### 1. Contare quanti iscritti ci sono stati ogni anno
```sql
SELECT YEAR(`enrolment_date`) AS `year`, COUNT(`id`) AS `students_count`
FROM `students`
GROUP BY YEAR(`enrolment_date`);
```

### 2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio
```sql
SELECT `office_address`, COUNT(`id`) AS `teachers_offices`
FROM `teachers`
GROUP BY `office_address`;
```

### 3. Calcolare la media dei voti di ogni appello d'esame
```sql
SELECT `exam_id`, AVG(`vote`) AS `average_vote`
FROM `exam_student`
GROUP BY `exam_id`;
```

### 4. Contare quanti corsi di laurea ci sono per ogni dipartimento
```sql
SELECT `department_id`, COUNT(`id`) AS `degrees_number`
FROM `degrees`
GROUP BY `department_id`;
```

# Query con JOIN

### 1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia
```sql
SELECT `students`.*
FROM `students`
INNER JOIN `degrees`
ON `degrees`.`id` = `students`.`degree_id`
WHERE `degrees`.`name` = 'Corso di Laurea in Economia';
```

### 2. Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di Neuroscienze
```sql
SELECT `degrees`.*
FROM `degrees`
INNER JOIN `departments`
ON `departments`.`id` = `degrees`.`department_id`
WHERE `level` = 'Magistrale'
AND `departments`.`name` = 'Dipartimento di Neuroscienze';
```

### 3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)
```sql
SELECT `teacher_id`,`courses`.*
FROM `course_teacher`
INNER JOIN `courses`
ON `courses`.`id` = `course_teacher`.`course_id`
WHERE `teacher_id` = 44;
```

### 4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e nome
```sql
SELECT `students`.`id`, `students`.`name`, `surname`, `date_of_birth`, `fiscal_code`, `enrolment_date`, `registration_number`, `students`.`email`, `degrees`.`name` AS `degree`, `departments`.`name` AS `department`
FROM `students`
INNER JOIN `degrees`
ON `degrees`.`id` = `students`.`degree_id`
INNER JOIN `departments`
ON `departments`.`id` = `degrees`.`department_id`
ORDER BY `surname`, `students`.`name` ASC;
```

### 5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti
```sql
SELECT `degrees`.`id`, `degrees`.`name`, `level`, `courses`.`name` AS `course`, `description`, `period`, `year`, `cfu`, `teachers`.`name` AS `teacher_name`, `surname` AS `teacher_surname`
FROM `degrees`
INNER JOIN `courses`
ON `courses`.`degree_id` = `degrees`.`id`
INNER JOIN `course_teacher`
ON `course_teacher`.`course_id` = `courses`.`id`
INNER JOIN `teachers`
ON `teachers`.`id` = `course_teacher`.`teacher_id`
ORDER BY `id`, `year`, `period`, `course` ASC;
```

### 6. Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)
```sql
SELECT DISTINCT `teachers`.*, `departments`.`name` AS `department`
FROM `teachers`
INNER JOIN `course_teacher`
ON `course_teacher`.`teacher_id` = `teachers`.`id`
INNER JOIN `courses`
ON `courses`.`id` = `course_teacher`.`course_id`
INNER JOIN `degrees`
ON `degrees`.`id` = `courses`.`degree_id`
INNER JOIN `departments`
ON `departments`.`id` = `degrees`.`department_id`
WHERE `departments`.`name` = 'Dipartimento di Matematica';
```

### 7. Selezionare per ogni studente quanti tentativi d’esame ha sostenuto per superare ciascuno dei suoi esami
```sql
SELECT `students`.*, `course_id`, `courses`.`name` AS `course_name`, COUNT(`exam_id`) AS `tries`
FROM `students`
INNER JOIN `exam_student`
ON `exam_student`.`student_id` = `students`.`id`
INNER JOIN `exams`
ON `exams`.`id` = `exam_student`.`exam_id`
INNER JOIN `courses`
ON `courses`.`id` = `exams`.`course_id`
GROUP BY `course_id`, `students`.`id`
ORDER BY `students`.`id` ASC;
```