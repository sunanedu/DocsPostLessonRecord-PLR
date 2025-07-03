# DocsPostLessonRecord:PLR
คู่มือเว็บแอพระบบบันทึกหลังการสอน

# ตัวอย่างการใช้งาน Endpoints

## 1. ผู้ใช้ทั่วไป (แขก ผู้เยี่ยมชม)
### สมัครสมาชิก
- **URL**: `POST http://127.0.0.1/plr/api/auth/register`
- **Request Body**:
  ```json
  {
      "citizen_id": "1234567890123",
      "prefix": "นาย",
      "first_name": "สมชาย",
      "last_name": "ใจดี",
      "department_id": 1,
      "college_id": 1,
      "email": "somchai@example.com"
  }
  ```
- **Response (Success)**: `201 Created`
```json
{"message": "User registered successfully"}
```

### เข้าสู่ระบบ
- **URL**: `POST http://127.0.0.1/plr/api/auth/login`
- **Request Body**:
  ```json
  {
      "citizen_id": "1234567890123",
      "password": "1234567890123"
  }
  ```
- **Response (Success)**: `200 OK` 
```json
{"token": "jwt_token", "roles": ["teacher"], "active_role": "teacher"}
```

## 2. ครู (role_id: 2, 'teacher')
### ดูข้อมูลตนเอง
- **URL**: `GET http://127.0.0.1/plr/api/users`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` 
```json
{"user_id": 1, "citizen_id": "1234567890123", ...}
```

### แก้ไขข้อมูลตนเอง
- **URL**: `PUT http://127.0.0.1/plr/api/users`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {
      "first_name": "สมชาย 2",
      "email": "somchai2@example.com"
  }
  ```
- **Response (Success)**: `200 OK`
  ```json
  {"message": "Profile updated successfully"}
  ```

### ลบข้อมูลตนเอง
- **URL**: `DELETE http://127.0.0.1/plr/api/users`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK`
```json
{"message": "Profile disabled successfully"}
```

### เปลี่ยนรหัสผ่าน
- **URL**: `POST http://127.0.0.1/plr/api/auth/change-password`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {
      "citizen_id": "1234567890123",
      "current_password": "1234567890123",
      "new_password": "newpassword123"
  }
  ```
- **Response (Success)**: `200 OK` 
```json
{"message": "Password changed successfully"}
```

### ดูวิชาทั้งหมด
- **URL**: `GET http://127.0.0.1/plr/api/courses?user_id=1&semester_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` 
```json
[{ "course_id": 1, "course_code": "CS101", ... }]
```

### ดูวิชาเฉพาะ id
- **URL**: `GET http://127.0.0.1/plr/api/courses/1?user_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` 
```json
{"course_id": 1, "course_code": "CS101", ...}
```

### เพิ่มวิชา (หลายรายการ)
- **URL**: `POST http://127.0.0.1/plr/api/courses`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  [
      {"course_code": "CS101", "course_name": "Intro to CS", "user_id": 1},
      {"course_code": "CS102", "course_name": "Advanced CS", "user_id": 1}
  ]
  ```
- **Response (Success)**: `201 Created`  
```json
{"message": "Course created successfully"}
```

### แก้ไขวิชา (หลายรายการ)
- **URL**: `PUT http://127.0.0.1/plr/api/courses`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  [
      {"course_id": 1, "course_name": "Intro to CS Updated"},
      {"course_id": 2, "course_name": "Advanced CS Updated"}
  ]
  ```
- **Response (Success)**: `200 OK` + `{"message": "Course updated successfully"}`

### ลบวิชา
- **URL**: `DELETE http://127.0.0.1/plr/api/courses`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {"course_ids": [1, 2]}
  ```
- **Response (Success)**: `200 OK` + 
```json
{"message": "2 courses deleted successfully"}
```

### ดูบันทึกทั้งหมด
- **URL**: `GET http://127.0.0.1/plr/api/records?user_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` 
```json
[{ "record_id": 1, "course_id": 1, ... }]
```

### ดูบันทึกเฉพาะ id
- **URL**: `GET http://127.0.0.1/plr/api/records/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` 
```json
{"record_id": 1, "course_id": 1, ...}
```

### บันทึกหลังการสอน (หลายรายการ)
- **URL**: `POST http://127.0.0.1/plr/api/records`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  [
      {
          "course_id": 1,
          "week_number": 1,
          "topic": "Lesson 1",
          "activity": "Lecture"
      },
      {
          "course_id": 1,
          "week_number": 2,
          "topic": "Lesson 2",
          "activity": "Discussion"
      }
  ]
  ```
- **Response (Success)**: `201 Created` + `{"message": "Record created successfully"}`

### แก้ไขบันทึก (หลายรายการ)
- **URL**: `PUT http://127.0.0.1/plr/api/records`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  [
      {
          "record_id": 1,
          "topic": "Lesson 1 Updated"
      },
      {
          "record_id": 2,
          "topic": "Lesson 2 Updated"
      }
  ]
  ```
- **Response (Success)**: `200 OK` + `{"message": "Record updated successfully"}`

### ลบบันทึก
- **URL**: `DELETE http://127.0.0.1/plr/api/records/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"message": "Record deleted successfully"}`

### ดูบันทึกทั้งหมดของเทอม
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1?user_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "record_id": 1, "semester_id": 1, ... }]`

### ดูบันทึกของแผนก
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/department/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "record_id": 1, "department_id": 1, ... }]`

### ดูบันทึกของครู
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/department/1/teacher/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "record_id": 1, "user_id": 1, ... }]`

### สรุปบันทึกของครู
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/summary/teacher/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"teacher_id": 1, "completion": [...], "overall_percentage": "50.00%"}`

### สรุปบันทึกของแผนก
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/summary/department/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"department_id": 1, "completion": [...], "overall_percentage": "60.00%"}`

### ดึงตัวช่วยทั้งหมด
- **URL**: `GET http://127.0.0.1/plr/api/helpers?user_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "helper_id": 1, "field_name": "activity", ... }]`

### เพิ่มตัวช่วย (หลายรายการ)
- **URL**: `POST http://127.0.0.1/plr/api/helpers`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  [
      {"field_name": "activity", "content": "Lecture", "order_number": 1},
      {"field_name": "activity", "content": "Discussion", "order_number": 2}
  ]
  ```
- **Response (Success)**: `201 Created` + `{"message": "Helper created successfully"}`

### แก้ไขตัวช่วย (หลายรายการ)
- **URL**: `PUT http://127.0.0.1/plr/api/helpers`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  [
      {"helper_id": 1, "content": "Lecture Updated"},
      {"helper_id": 2, "content": "Discussion Updated"}
  ]
  ```
- **Response (Success)**: `200 OK` + `{"message": "Helper updated successfully"}`

### ดึงตัวช่วยของฟิลด์
- **URL**: `GET http://127.0.0.1/plr/api/helpers/field/activity?user_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "helper_id": 1, "content": "Lecture", ... }]`

### ลบตัวช่วย
- **URL**: `DELETE http://127.0.0.1/plr/api/helpers/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"message": "Helper deleted successfully"}`

### อัพโหลดลายเซ็น
- **URL**: `POST http://127.0.0.1/plr/api/users/upload-signature`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**: `multipart/form-data` with `file` field (PNG/JPG < 8MB)
- **Response (Success)**: `201 Created` + `{"message": "Signature uploaded successfully", "signature": "random_citizen_id.png"}`

### ลบลายเซ็น
- **URL**: `DELETE http://127.0.0.1/plr/api/users/delete-signature`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**: (optional) `{"user_id": 1}`
- **Response (Success)**: `200 OK` + `{"message": "Signature deleted successfully"}`

## 3. หัวหน้าแผนก (role_id: 3, 'department-head')
### ดูบันทึกของเทอม
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1?department_id=1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "record_id": 1, "semester_id": 1, ... }]`

### ดูบันทึกของแผนก
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/department/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "record_id": 1, "department_id": 1, ... }]`

### ดูบันทึกของครู
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/department/1/teacher/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "record_id": 1, "user_id": 1, ... }]`

### สรุปบันทึกของครู
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/summary/teacher/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"teacher_id": 1, "completion": [...], "overall_percentage": "50.00%"}`

### สรุปบันทึกของแผนก
- **URL**: `GET http://127.0.0.1/plr/api/records/semesters/1/summary/department/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"department_id": 1, "completion": [...], "overall_percentage": "60.00%"}`

### ดูอนุมัติทั้งหมด
- **URL**: `GET http://127.0.0.1/plr/api/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "approval_id": 1, "record_id": 1, ... }]`

### ดูอนุมัติของเทอม
- **URL**: `GET http://127.0.0.1/plr/api/approvals/semesters/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "approval_id": 1, "semester_id": 1, ... }]`

### อนุมัติบันทึก (หลายรายการ)
- **URL**: `POST http://127.0.0.1/plr/api/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {
      "record_ids": [1, 2],
      "status": "approved",
      "comment": "Approved by department head"
  }
  ```
- **Response (Success)**: `201 Created` + `{"message": "2 approvals created successfully"}`

### แก้ไขอนุมัติ (หลายรายการ)
- **URL**: `PUT http://127.0.0.1/plr/api/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {
      "record_ids": [1, 2],
      "status": "approved",
      "comment": "Updated comment"
  }
  ```
- **Response (Success)**: `200 OK` + `{"message": "2 approvals updated or created successfully"}`

### ลบอนุมัติ (เฉพาะ id)
- **URL**: `DELETE http://127.0.0.1/plr/api/approvals/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"message": "Approval deleted successfully"}`

### ลบอนุมัติ (หลายรายการ)
- **URL**: `DELETE http://127.0.0.1/plr/api/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {"approval_ids": [1, 2]}
  ```
- **Response (Success)**: `200 OK` + `{"message": "2 approvals deleted successfully"}`

## 4. รองผู้อำนวยการ/เลขา (role_id: 4, 'director')
### ดูอนุมัติทั้งหมด
- **URL**: `GET http://127.0.0.1/plr/api/director/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "approval_id": 1, "record_id": 1, ... }]`

### ดูอนุมัติของเทอม
- **URL**: `GET http://127.0.0.1/plr/api/director/approvals/semesters/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "approval_id": 1, "semester_id": 1, ... }]`

### ดูอนุมัติของแผนก
- **URL**: `GET http://127.0.0.1/plr/api/director/approvals/semesters/1/department/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "approval_id": 1, "department_id": 1, ... }]`

### ดูอนุมัติของครู
- **URL**: `GET http://127.0.0.1/plr/api/director/approvals/semesters/1/department/1/teacher/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `[{ "approval_id": 1, "user_id": 1, ... }]`

### อนุมัติบันทึก (หลายรายการ)
- **URL**: `POST http://127.0.0.1/plr/api/director/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {
      "record_ids": [1, 2],
      "director_status": "approved",
      "director_comment": "Approved by director"
  }
  ```
- **Response (Success)**: `201 Created` + `{"message": "2 approvals created or updated successfully"}`

### แก้ไขอนุมัติ (หลายรายการ)
- **URL**: `PUT http://127.0.0.1/plr/api/director/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {
      "record_ids": [1, 2],
      "director_status": "approved",
      "director_comment": "Updated comment"
  }
  ```
- **Response (Success)**: `200 OK` + `{"message": "2 approvals updated successfully"}`

### ลบอนุมัติ (เฉพาะ id)
- **URL**: `DELETE http://127.0.0.1/plr/director/api/approvals/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"message": "Approval deleted successfully"}`

### ลบอนุมัติ (หลายรายการ)
- **URL**: `DELETE http://127.0.0.1/plr/director/api/approvals`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Request Body**:
  ```json
  {"approval_ids": [1, 2]}
  ```
- **Response (Success)**: `200 OK` + `{"message": "2 approvals deleted successfully"}`

### สรุปบันทึกของครู
- **URL**: `GET http://127.0.0.1/plr/api/director/approvals/semesters/1/summary/teacher/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"teacher_id": 1, "completion": [...], "overall_percentage": "50.00%"}`

### สรุปบันทึกของแผนก
- **URL**: `GET http://127.0.0.1/plr/api/director/approvals/semesters/1/summary/department/1`
- **Headers**: `Authorization: Bearer <jwt_token>`
- **Response (Success)**: `200 OK` + `{"department_id": 1, "completion": [...], "overall_percentage": "60.00%"}`

## 5. ผู้ดูแลระบบ (role_id: 1, 'admin')
### จัดการข้อมูลตึก
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/buildings` → `200 OK` + `[{ "building_id": 1, "building_name": "Building A", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/buildings/1` → `200 OK` + `{"building_id": 1, "building_name": "Building A", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/buildings` + `[{"building_name": "Building A", "building_code": "A"}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/buildings` + `[{"building_id": 1, "building_name": "Building A Updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/buildings/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/buildings` + `{"building_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลห้องเรียน
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/classrooms` → `200 OK` + `[{ "classroom_id": 1, "classroom_name": "Room 101", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/classrooms/1` → `200 OK` + `{"classroom_id": 1, "classroom_name": "Room 101", ...}`
- **ดูเฉพาะแผนก**: `GET http://127.0.0.1/plr/api/classrooms/department/1` → `200 OK` + `[{ "classroom_id": 1, "department_id": 1, ... }]`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/classrooms` + `[{"classroom_name": "Room 101", "building_id": 1}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/classrooms` + `[{"classroom_id": 1, "classroom_name": "Room 101 Updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/classrooms/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/classrooms` + `{"classroom_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลหลักสูตร
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/curriculums` → `200 OK` + `[{ "curriculum_id": 1, "curriculum_name": "Curriculum A", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/curriculums/1` → `200 OK` + `{"curriculum_id": 1, "curriculum_name": "Curriculum A", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/curriculums` + `[{"curriculum_code": "CUR1", "curriculum_name": "Curriculum A", "user_id": 1}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/curriculums` + `[{"curriculum_id": 1, "curriculum_name": "Curriculum A Updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/curriculums/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/curriculums` + `{"curriculum_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลแผนก
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/departments` → `200 OK` + `[{ "department_id": 1, "department_name": "Dept A", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/departments/1` → `200 OK` + `{"department_id": 1, "department_name": "Dept A", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/departments` + `[{"department_name": "Dept A"}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/departments` + `[{"department_id": 1, "department_name": "Dept A Updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/departments/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/departments` + `{"department_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลกลุ่มผู้เรียน
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/groups` → `200 OK` + `[{ "group_id": 1, "group_name": "Group A", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/groups/1` → `200 OK` + `{"group_id": 1, "group_name": "Group A", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/groups` + `[{"group_name": "Group A", "user_id": 1}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/groups` + `[{"group_id": 1, "group_name": "Group A Updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/groups/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/groups` + `{"group_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลสิทธิ
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/roles` → `200 OK` + `[{ "role_id": 1, "role_name": "admin", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/roles/1` → `200 OK` + `{"role_id": 1, "role_name": "admin", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/roles` + `[{"role_name": "new_role"}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/roles` + `[{"role_id": 1, "role_name": "admin_updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/roles/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/roles` + `{"role_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลภาคเรียน
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/semesters` → `200 OK` + `[{ "semester_id": 1, "semester_name": "Semester 1", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/semesters/1` → `200 OK` + `{"semester_id": 1, "semester_name": "Semester 1", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/semesters` + `[{"semester_name": "Semester 1", "start_date": "2025-01-01", "end_date": "2025-06-30"}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/semesters` + `[{"semester_id": 1, "semester_name": "Semester 1 Updated"}, ...]` → `200 OK`
- **ลบ (เฉพาะ id)**: `DELETE http://127.0.0.1/plr/api/semesters/1` → `200 OK`
- **ลบ (หลายรายการ)**: `DELETE http://127.0.0.1/plr/api/semesters` + `{"semester_ids": [1, 2]}` → `200 OK`

### จัดการข้อมูลวิทยาลัย
- **ดูทั้งหมด**: `GET http://127.0.0.1/plr/api/colleges` → `200 OK` + `[{ "college_id": 1, "college_name": "College A", ... }]`
- **ดูเฉพาะ id**: `GET http://127.0.0.1/plr/api/colleges/1` → `200 OK` + `{"college_id": 1, "college_name": "College A", ...}`
- **เพิ่ม (หลายรายการ)**: `POST http://127.0.0.1/plr/api/colleges` + `[{"college_name": "College A"}, ...]` → `201 Created`
- **แก้ไข (หลายรายการ)**: `PUT http://127.0.0.1/plr/api/colleges` + `[{"college_id": 1, "college_name": "College A Updated"}, ...]` → `200 OK`

### รายงานสรุป
- **ดูสรุป**: `GET http://127.0.0.1/plr/api/reports/summary?semester_id=1` → `200 OK` + `{"summary": {...}}`