# 九楼大屏周末数据更新需求

------
### 1. 创建历史备份表
```sql
CREATE TABLE `exam_his` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `total_reading_num` bigint(20) DEFAULT NULL COMMENT '累计阅卷量',
  `mandarin_serve_num` bigint(20) DEFAULT NULL COMMENT '普通话服务人数',
  `english_serve_num` bigint(20) DEFAULT NULL COMMENT '英语听说考试-累计服务人数',
  `mhk_serve_num` bigint(20) DEFAULT NULL COMMENT '民族地区汉考MHK-累计服务人数',
  `exam_total` bigint(20) DEFAULT NULL COMMENT '校内考试总数',
  `total_serve_school` bigint(20) DEFAULT NULL COMMENT '累计服务学校',
  `total_test_num` bigint(20) DEFAULT NULL COMMENT '累计测试人数',
  `total_reading_num_week` bigint(20) DEFAULT NULL COMMENT '累计阅卷量（近七天）',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='智慧考试-历史表';

CREATE TABLE `manage_his` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `province_construct_num` bigint(20) DEFAULT NULL COMMENT '省平台建设',
  `city_construct_num` bigint(20) DEFAULT NULL COMMENT '地市平台建设',
  `country_construct_num` bigint(20) DEFAULT NULL COMMENT '区县平台建设',
  `total_class` bigint(20) DEFAULT NULL COMMENT '累计排课次数',
  `total_attendance` bigint(20) DEFAULT NULL COMMENT '累计考勤次数',
  `total_interactive` bigint(20) DEFAULT NULL COMMENT '家校互动次数',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='智慧管理-历史表';

CREATE TABLE `resource_his` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `resource_num` bigint(20) DEFAULT NULL COMMENT '教学资源总数',
  `increase_num` bigint(20) DEFAULT NULL COMMENT '新增数（月）',
  `teaching_design_num` bigint(20) DEFAULT NULL COMMENT '教学设计',
  `teaching_file_num` bigint(20) DEFAULT NULL COMMENT '教学课件',
  `media_material_num` bigint(20) DEFAULT NULL COMMENT '多媒体素材',
  `test_evaluate_num` bigint(20) DEFAULT NULL COMMENT '测试与评价',
  `exercise_num` bigint(20) DEFAULT NULL COMMENT '习题资源总数-道',
  `exercise_packge_num` bigint(20) DEFAULT NULL COMMENT '习题资源总数-套',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='资源生态-历史表';

CREATE TABLE `teaching_his` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `teacher_serve_num` bigint(20) DEFAULT NULL COMMENT '服务人数-教师',
  `student_serve_num` bigint(20) DEFAULT NULL COMMENT '服务人数-学生',
  `teacher_use_num` bigint(20) DEFAULT NULL COMMENT '使用次数-教师',
  `student_use_num` bigint(20) DEFAULT NULL COMMENT '使用次数-学生',
  `teaching_num` bigint(20) DEFAULT NULL COMMENT '课堂授课',
  `interactive_num` bigint(20) DEFAULT NULL COMMENT '课堂互动',
  `homework_num` bigint(20) DEFAULT NULL COMMENT '作业布置',
  `exam_answer_num` bigint(20) DEFAULT NULL COMMENT '试卷讲评',
  `intel_subject_num` bigint(20) DEFAULT NULL COMMENT '智能推题',
  `intel_testpaper_num` bigint(20) DEFAULT NULL COMMENT '智能评卷',
  `report_num` bigint(20) DEFAULT NULL COMMENT '报告生成',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='智慧教学-历史表';
```

### 2. 创建存储过程
> * updateResourceData（每周日24点更新）
```sql
CREATE PROCEDURE `updateResourceData`()
BEGIN
  set @inc_resource_num_min = 24300;
  set @inc_resource_num_max = 24359;
  set @inc_increase_num_min = 2;
  set @inc_increase_num_max = 3;
  set @inc_teaching_design_num_min = 120;
  set @inc_teaching_design_num_max = 126;
  set @inc_teaching_file_num_min = 120;
  set @inc_teaching_file_num_max = 130;
  set @inc_media_material_num_min = 720;
  set @inc_media_material_num_max = 731;
  set @inc_test_evaluate_num_min = 180;
  set @inc_test_evaluate_num_max = 185;
  set @inc_exercise_num_min = 80;
  set @inc_exercise_num_max = 87;
  set @inc_exercise_packge_num_min = 22900;
  set @inc_exercise_packge_num_max = 22982;
  
	INSERT INTO resource_his(resource_num, increase_num, teaching_design_num, teaching_file_num, media_material_num,
	                         test_evaluate_num, exercise_num, exercise_packge_num) 
	SELECT resource_num, increase_num, teaching_design_num, teaching_file_num, media_material_num,
	                         test_evaluate_num, exercise_num, exercise_packge_num FROM resource WHERE id = 1;
  UPDATE resource SET
    resource_num = resource_num + FLOOR(@inc_resource_num_min + (RAND() * (@inc_resource_num_max - @inc_resource_num_min + 1))),
	increase_num = increase_num + FLOOR(@inc_increase_num_min + (RAND() * (@inc_increase_num_max - @inc_increase_num_min + 1))),
	teaching_design_num = teaching_design_num + FLOOR(@inc_teaching_design_num_min + (RAND() * (@inc_teaching_design_num_max - @inc_teaching_design_num_min + 1))),
	teaching_file_num = teaching_file_num + FLOOR(@inc_teaching_file_num_min + (RAND() * (@inc_teaching_file_num_max - @inc_teaching_file_num_min + 1))),
	media_material_num = media_material_num + FLOOR(@inc_media_material_num_min + (RAND() * (@inc_media_material_num_max - @inc_media_material_num_min + 1))),
	test_evaluate_num = test_evaluate_num + FLOOR(@inc_test_evaluate_num_min + (RAND() * (@inc_test_evaluate_num_max - @inc_test_evaluate_num_min + 1))),
	exercise_num = exercise_num + FLOOR(@inc_exercise_num_min + (RAND() * (@inc_exercise_num_max - @inc_exercise_num_min + 1))),
	exercise_packge_num = exercise_packge_num + FLOOR(@inc_exercise_packge_num_min + (RAND() * (@inc_exercise_packge_num_max - @inc_exercise_packge_num_min + 1))),
	update_time = CURRENT_TIMESTAMP
  WHERE 1 = 1;
  
  COMMIT;
END;
```

> * updateScreenData（每周六、日，10、16点更新）
```sql
CREATE PROCEDURE `updateScreenData`()
BEGIN
  set @inc_exam_total_min = 900;
  set @inc_exam_total_max = 919;
  set @inc_total_serve_school_min = 4;
  set @inc_total_serve_school_max = 6;
  set @inc_total_test_num_min = 1800;
  set @inc_total_test_num_max = 1840;
  set @inc_teacher_serve_num_min = 30;
  set @inc_teacher_serve_num_max = 39;
  set @inc_student_serve_num_min = 300;
  set @inc_student_serve_num_max = 344;
  set @inc_teacher_use_num_min = 80000;
  set @inc_teacher_use_num_max = 80524;
  set @inc_student_use_num_min = 654000;
  set @inc_student_use_num_max = 654703;
  set @inc_teaching_num_min = 3200;
  set @inc_teaching_num_max = 3248;
  set @inc_interactive_num_min = 6000;
  set @inc_interactive_num_max = 6152;
  set @inc_homework_num_min = 41000;
  set @inc_homework_num_max = 41302;
  set @inc_exam_answer_num_min = 3100;
  set @inc_exam_answer_num_max = 3180;
  set @inc_total_attendance_min = 102000;
  set @inc_total_attendance_max = 102181;
  set @inc_total_interactive_min = 9100;
  set @inc_total_interactive_max = 9168;
  
	INSERT INTO exam_his(total_reading_num, mandarin_serve_num, english_serve_num, mhk_serve_num,
	                     exam_total, total_serve_school, total_test_num, total_reading_num_week) 
	SELECT total_reading_num, mandarin_serve_num, english_serve_num, mhk_serve_num, 
	                     exam_total, total_serve_school, total_test_num, total_reading_num_week FROM exam WHERE id = 1;
  -- 更新exam表
  UPDATE exam SET 
      exam_total = exam_total + FLOOR(@inc_exam_total_min + (RAND() * (@inc_exam_total_max - @inc_exam_total_min + 1))),
	  total_serve_school = total_serve_school + FLOOR(@inc_total_serve_school_min + (RAND() * (@inc_total_serve_school_max - @inc_total_serve_school_min + 1))),
	  total_test_num = total_test_num + FLOOR(@inc_total_test_num_min + (RAND() * (@inc_total_test_num_max - @inc_total_test_num_min + 1))),
		update_time = CURRENT_TIMESTAMP
  where id = 1;
	
	INSERT INTO teaching_his(teacher_serve_num, student_serve_num, teacher_use_num, student_use_num, teaching_num,
	                         interactive_num, homework_num, exam_answer_num, intel_subject_num, intel_testpaper_num, report_num) 
	SELECT teacher_serve_num, student_serve_num, teacher_use_num, student_use_num, teaching_num,
	                         interactive_num, homework_num, exam_answer_num, intel_subject_num, intel_testpaper_num, report_num FROM teaching WHERE id = 1;
  -- 更新teaching表
  UPDATE teaching SET
	  teacher_serve_num = teacher_serve_num + FLOOR(@inc_teacher_serve_num_min + (RAND() * (@inc_teacher_serve_num_max - @inc_teacher_serve_num_min + 1))),
	  student_serve_num = student_serve_num + FLOOR(@inc_student_serve_num_min + (RAND() * (@inc_student_serve_num_max - @inc_student_serve_num_min + 1))),
	  teacher_use_num =  teacher_use_num + FLOOR(@inc_teacher_use_num_min + (RAND() * (@inc_teacher_use_num_max - @inc_teacher_use_num_min + 1))),
	  student_use_num =  student_use_num + FLOOR(@inc_student_use_num_min + (RAND() * (@inc_student_use_num_max - @inc_student_use_num_min + 1))),
	  teaching_num =  teaching_num + FLOOR(@inc_teaching_num_min + (RAND() * (@inc_teaching_num_max - @inc_teaching_num_min + 1))),
	  interactive_num = interactive_num + FLOOR(@inc_interactive_num_min + (RAND() * (@inc_interactive_num_max - @inc_interactive_num_min + 1))),
	  homework_num = homework_num + FLOOR(@inc_homework_num_min + (RAND() * (@inc_homework_num_max - @inc_homework_num_min + 1))),
	  exam_answer_num =  exam_answer_num + FLOOR(@inc_exam_answer_num_min + (RAND() * (@inc_exam_answer_num_max - @inc_exam_answer_num_min + 1))),
		update_time = CURRENT_TIMESTAMP
  where id = 1;
  
	INSERT INTO manage_his(province_construct_num, city_construct_num, country_construct_num,
	                       total_class, total_attendance, total_interactive) 
	SELECT province_construct_num, city_construct_num, country_construct_num,
	                       total_class, total_attendance, total_interactive FROM manage WHERE id = 1;
  -- 更新manage表
  UPDATE manage SET
	  total_attendance = total_attendance + FLOOR(@inc_total_attendance_min + (RAND() * (@inc_total_attendance_max - @inc_total_attendance_min + 1))),
	  total_interactive =  total_interactive + FLOOR(@inc_total_interactive_min + (RAND() * (@inc_total_interactive_max - @inc_total_interactive_min + 1))),
		update_time = CURRENT_TIMESTAMP
  where id = 1;
 
  commit;
END;
```

### 3. 查看定时任务开关是否开启
> show VARIABLES like '%event_scheduler%';

### 4. 开启定时任务
> set GLOBAL event_scheduler=1;
>
> **注意：此处开启方式会在mysql数据库重启后失效，如果权限允许，建议修改mysql配置文件**

### 5. 创建定时事件
```sql
CREATE EVENT `updateResourceDataOnSundayEve` 
ON SCHEDULE EVERY 1 WEEK 
STARTS '2019-04-08 00:00:00' 
ON COMPLETION NOT PRESERVE ENABLE 
DO 
call `updateResourceData`;
```
```sql
CREATE EVENT `updateScreenDataOnSaturdayMoring` 
ON SCHEDULE EVERY 1 WEEK 
STARTS '2019-04-06 10:00:00' 
ON COMPLETION NOT PRESERVE ENABLE 
DO 
call `updateScreenData`;
```
```sql
CREATE EVENT `updateScreenDataOnSaturdayAfternoon` 
ON SCHEDULE EVERY 1 WEEK 
STARTS '2019-04-06 16:00:00' 
ON COMPLETION NOT PRESERVE ENABLE 
DO 
call `updateScreenData`;
```
```sql
CREATE EVENT `updateScreenDataOnSundayMorning` 
ON SCHEDULE EVERY 1 WEEK 
STARTS '2019-04-07 10:00:00' 
ON COMPLETION NOT PRESERVE ENABLE 
DO 
call `updateScreenData`;
```
```sql
CREATE EVENT `updateScreenDataOnSundayAfternoon` 
ON SCHEDULE EVERY 1 WEEK 
STARTS '2019-04-07 16:00:00' 
ON COMPLETION NOT PRESERVE ENABLE 
DO 
call `updateScreenData`;
```

### 6. 验证跟踪方案

> * 在对应的时间点分别查看业务表和历史备份表的数据变化，各表中的update_time字段将是可直观了解数据表信息的关键指标。

> * SELECT * FROM information_schema.EVENTS; 语句可直接查看mysql event执行情况。

