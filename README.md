# คู่มือการใช้งาน API ระบบ Post Lesson Record (PLR)
## อัพเดท 3 กรกฎาคม 2025 16.30น.

เอกสารนี้ให้รายละเอียดการใช้งาน API ทั้งหมดของระบบ Post Lesson Record (PLR) โดยออกแบบตามมาตรฐานระดับสากล เช่น LINE และบริษัทชั้นนำอื่น ๆ ครอบคลุมวิธีการใช้งาน Endpoint, Method, URI, Request Headers, Request Body, Response, รวมถึงการใช้ Bearer Token และ X-Active-Role สำหรับการระบุบทบาทผู้ใช้ (Guest, Teacher, Department Head, Director, Admin)

---

## 1. แขก (Guest - ผู้เยี่ยมชม)
ผู้ใช้ที่ยังไม่ได้ลงทะเบียนหรือล็อกอิน

### 1.1 สมัครสมาชิก
- **Method**: POST
- **URI**: `http://127.0.0.1/plr/api/auth/register`
- **Description**: ใช้สำหรับสร้างบัญชีผู้ใช้ใหม่
- **Request Headers**:
  ```
  Content-Type: application/json
  ```
- **Request Body**:
  ```json
  {
    "citizen_id": "1101901234567",
    "prefix": "นาย",
    "first_name": "สมชาย",
    "last_name": "ใจดี",
    "email": "somchai@example.com",
    "department_id": 1,
    "college_id": 1
  }
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "user": {
        "user_id": 10,
        "citizen_id": "1101901234567",
        "role": "teacher"
      }
    }
    ```
  - **400 Bad Request**: ข้อมูลไม่ครบถ้วนหรือรูปแบบไม่ถูกต้อง (เช่น citizen_id ไม่ใช่ 13 หลัก)
  - **409 Conflict**: citizen_id ซ้ำในระบบ

### 1.2 เข้าสู่ระบบ
- **Method**: POST
- **URI**: `http://127.0.0.1/plr/api/auth/login`
- **Description**: ใช้สำหรับล็อกอินด้วย citizen_id
- **Request Headers**:
  ```
  Content-Type: application/json
  ```
- **Request Body**:
  ```json
  {
    "citizen_id": "1101901234567"
  }
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
    }
    ```
  - **401 Unauthorized**: citizen_id ไม่ถูกต้องหรือยังไม่ได้สมัคร

---

## 2. ครู (Role ID: 2, 'teacher')
ผู้ใช้ที่มีสิทธิ์ครู สามารถจัดการข้อมูลส่วนตัว, วิชา, บันทึกหลังการสอน และตัวช่วยกรอกข้อมูล

### 2.1 ดูข้อมูลตนเอง
- **Method**: GET
- **URI**: `http://127.0.0.1/plr/api/users`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: teacher
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "user_id": 2,
      "citizen_id": "1101901234568",
      "first_name": "อุดร",
      "last_name": "เศษโถ",
      "department_id": 1
    }
    ```
  - **401 Unauthorized**: Token ไม่ถูกต้องหรือหมดอายุ

### 2.2 แก้ไขข้อมูลตนเอง
- **Method**: PUT
- **URI**: `http://127.0.0.1/plr/api/users`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: teacher
  Content-Type: application/json
  ```
- **Request Body**:
  ```json
  {
    "first_name": "อุดร (อัพเดท)",
    "email": "udon.updated@example.com"
  }
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "message": "อัปเดตข้อมูลสำเร็จ",
      "user": {
        "user_id": 2,
        "first_name": "อุดร (อัพเดท)",
        "email": "udon.updated@example.com"
      }
    }
    ```
  - **400 Bad Request**: ข้อมูลไม่ถูกต้อง
  - **403 Forbidden**: ไม่มีสิทธิ์แก้ไข

### 2.3 ลบข้อมูลตนเอง
- **Method**: DELETE
- **URI**: `http://127.0.0.1/plr/api/users`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: teacher
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "message": "ลบข้อมูลสำเร็จ",
      "status": "disabled"
    }
    ```
  - **403 Forbidden**: ต้องมี admin อนุมัติ

### 2.4 เปลี่ยนรหัสผ่าน
- **Method**: POST
- **URI**: `http://127.0.0.1/plr/api/auth/change-password`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: teacher
  Content-Type: application/json
  ```
- **Request Body**:
  ```json
  {
    "old_password": "1101901234568",
    "new_password": "newpassword123"
  }
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "message": "เปลี่ยนรหัสผ่านสำเร็จ"
    }
    ```
  - **400 Bad Request**: รหัสผ่านเก่าไม่ถูกต้อง

### 2.5 ครู - วิชา
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/courses`
  - **Request Headers**:
    ```
    Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
    X-Active-Role: teacher
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "course_id": 1,
          "course_code": "31901-2002",
          "course_name": "ระบบปฏิบัติการเครื่องแม่ข่าย"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/courses/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "course_id": 1,
        "course_code": "31901-2002",
        "course_name": "ระบบปฏิบัติการเครื่องแม่ข่าย"
      }
      ```
    - **404 Not Found**: ไม่พบวิชา
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/courses`
  - **Request Body**:
    ```json
    [
      {
        "course_code": "NEW01",
        "course_name": "วิชาใหม่",
        "theory_hours": 30,
        "practice_hours": 30,
        "credit": 3,
        "total_hours": 60,
        "weeks": 15,
        "curriculum_id": 1,
        "semester_id": 1,
        "classroom_id": 101,
        "user_id": 2,
        "group_id": 1
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มวิชาสำเร็จ",
        "course_id": 15
      }
      ```
    - **400 Bad Request**: ข้อมูลไม่ถูกต้อง
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/courses`
  - **Request Body**:
    ```json
    [
      {
        "course_id": 15,
        "course_name": "วิชาใหม่ (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขวิชาสำเร็จ"
      }
      ```
    - **404 Not Found**: ไม่พบวิชา
- **ลบ**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/courses`
  - **Request Body**:
    ```json
    [15]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบวิชาสำเร็จ"
      }
      ```
    - **403 Forbidden**: ไม่มีสิทธิ์

### 2.6 ครู - บันทึกหลังการสอน
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "record_id": 1,
        "course_id": 1,
        "topic": "Introduction"
      }
      ```
    - **404 Not Found**: ไม่พบบันทึก
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/records`
  - **Request Body**:
    ```json
    [
      {
        "course_id": 1,
        "user_id": 2,
        "week_number": 1,
        "start_date": "2025-07-01",
        "end_date": "2025-07-07",
        "topic": "Introduction",
        "activity": "Lecture",
        "behavior": "Good",
        "work_principle": "Teamwork"
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มบันทึกสำเร็จ",
        "record_id": 11
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/records`
  - **Request Body**:
    ```json
    [
      {
        "record_id": 11,
        "topic": "Introduction (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขบันทึกสำเร็จ"
      }
      ```
- **ลบ**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/records/11`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบบันทึกสำเร็จ"
      }
      ```
    - **403 Forbidden**: ไม่มีสิทธิ์

### 2.7 ครู - แสดงผลบันทึกย้อนหลัง (เฉพาะ admin สามารถแก้ไข/ลบ)
- **ดูทั้งหมดของ semester**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **ดูตาม department**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **ดูตาม teacher**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/department/1/teacher/2`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **สรุปตาม teacher**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/summary/teacher/2`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "teacher_id": 2,
        "total_records": 5
      }
      ```
- **สรุปตาม department (USER_VIEW=1)**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/summary/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "department_id": 1,
        "total_records": 10
      }
      ```
    - **403 Forbidden**: USER_VIEW=0

### 2.8 ครู - ตัวช่วย/ตัวเลือก
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/helpers`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "helper_id": 1,
          "field_name": "activity",
          "content": "Lecture"
        }
      ]
      ```
- **ดึงตามฟิลด์**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/helpers/field/activity`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "helper_id": 1,
          "content": "Lecture"
        }
      ]
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/helpers`
  - **Request Body**:
    ```json
    [
      {
        "user_id": 2,
        "field_name": "activity",
        "content": "New Activity",
        "order_number": 1
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มตัวช่วยสำเร็จ",
        "helper_id": 4
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/helpers`
  - **Request Body**:
    ```json
    [
      {
        "helper_id": 4,
        "content": "Updated Activity"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขตัวช่วยสำเร็จ"
      }
      ```
- **ลบ**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/helpers/4`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบตัวช่วยสำเร็จ"
      }
      ```

---

## 3. หัวหน้าแผนก (Role ID: 3, 'department-head')
จัดการข้อมูลบันทึกและการอนุมัติ

### 3.1 ดูบันทึกตามเทอม
- **ดูทั้งหมดของ semester**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **ดูตาม department**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **ดูตาม teacher**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/department/1/teacher/2`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "record_id": 1,
          "course_id": 1,
          "topic": "Introduction"
        }
      ]
      ```
- **สรุปตาม teacher**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/summary/teacher/2`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "teacher_id": 2,
        "total_records": 5
      }
      ```
- **สรุปตาม department**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/records/semesters/1/summary/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "department_id": 1,
        "total_records": 10
      }
      ```

### 3.2 อนุมัติ
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/approvals`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "approval_id": 1,
          "record_id": 1,
          "status": "approved"
        }
      ]
      ```
- **ดูตาม semester**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/approvals/semesters/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "approval_id": 1,
          "record_id": 1,
          "status": "approved"
        }
      ]
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/approvals`
  - **Request Body**:
    ```json
    [
      {
        "record_id": 1,
        "user_id": 1,
        "comment": "อนุมัติแล้ว",
        "status": "approved"
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มการอนุมัติสำเร็จ",
        "approval_id": 3
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/approvals`
  - **Request Body**:
    ```json
    [
      {
        "approval_id": 3,
        "comment": "อัพเดตการอนุมัติ"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขการอนุมัติสำเร็จ"
      }
      ```
- **ลบ (เฉพาะของตนเอง)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/approvals/3`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบการอนุมัติสำเร็จ"
      }
      ```
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/approvals`
  - **Request Body**:
    ```json
    [3]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบการอนุมัติสำเร็จ"
      }
      ```

---

## 4. รองผู้อำนวยการ/เลขา (Role ID: 4, 'director')
จัดการการอนุมัติขั้นสูง

### 4.1 ดูการอนุมัติ
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/director/approvals`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "approval_id": 1,
          "record_id": 1,
          "status": "approved",
          "director_status": "pending"
        }
      ]
      ```
- **ดูตาม semester**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/director/approvals/semesters/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "approval_id": 1,
          "record_id": 1,
          "status": "approved",
          "director_status": "pending"
        }
      ]
      ```
- **ดูตาม department**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/director/approvals/semesters/1/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "approval_id": 1,
          "record_id": 1,
          "status": "approved",
          "director_status": "pending"
        }
      ]
      ```
- **ดูตาม teacher**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/director/approvals/semesters/1/department/1/teacher/2`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "approval_id": 1,
          "record_id": 1,
          "status": "approved",
          "director_status": "pending"
        }
      ]
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/director/approvals`
  - **Request Body**:
    ```json
    [
      {
        "record_id": 1,
        "user_id": 9,
        "comment": "อนุมัติจากผู้อำนวยการ",
        "director_status": "approved"
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มการอนุมัติสำเร็จ",
        "approval_id": 4
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/director/approvals`
  - **Request Body**:
    ```json
    [
      {
        "approval_id": 4,
        "director_comment": "อัพเดตการอนุมัติ"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขการอนุมัติสำเร็จ"
      }
      ```
- **ลบ (เปลี่ยนสถานะ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/director/api/approvals/4`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "เปลี่ยนสถานะการอนุมัติสำเร็จ"
      }
      ```
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/director/api/approvals`
  - **Request Body**:
    ```json
    [4]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "เปลี่ยนสถานะการอนุมัติสำเร็จ"
      }
      ```
- **สรุปตาม teacher**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/director/approvals/semesters/1/summary/teacher/2`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "teacher_id": 2,
        "approved_records": 3
      }
      ```
- **สรุปตาม department**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/director/approvals/semesters/1/summary/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "department_id": 1,
        "approved_records": 8
      }
      ```

---

## 5. ผู้ดูแลระบบ (Role ID: 1, 'admin')
จัดการข้อมูลพื้นฐาน

### 5.1 อาคาร
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/buildings`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "building_id": 1,
          "building_name": "อาคาร 1"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/buildings/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "building_id": 1,
        "building_name": "อาคาร 1"
      }
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/buildings`
  - **Request Body**:
    ```json
    [
      {
        "building_name": "อาคาร 6"
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มอาคารสำเร็จ",
        "building_id": 6
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/buildings`
  - **Request Body**:
    ```json
    [
      {
        "building_id": 6,
        "building_name": "อาคาร 6 (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขอาคารสำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/buildings/6`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบอาคารสำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/buildings`
  - **Request Body**:
    ```json
    [6]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบอาคารสำเร็จ"
      }
      ```

### 5.2 ห้องเรียน
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/classrooms`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "classroom_id": 101,
          "classroom_name": "ห้อง T201"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/classrooms/101`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "classroom_id": 101,
        "classroom_name": "ห้อง T201"
      }
      ```
- **ดูตาม department**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/classrooms/department/1`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "classroom_id": 101,
          "classroom_name": "ห้อง T201"
        }
      ]
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/classrooms`
  - **Request Body**:
    ```json
    [
      {
        "classroom_name": "ห้อง T401",
        "floor": 4,
        "building_id": 1,
        "department_id": 1
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มห้องเรียนสำเร็จ",
        "classroom_id": 106
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/classrooms`
  - **Request Body**:
    ```json
    [
      {
        "classroom_id": 106,
        "classroom_name": "ห้อง T401 (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขห้องเรียนสำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/classrooms/106`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบห้องเรียนสำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/classrooms`
  - **Request Body**:
    ```json
    [106]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบห้องเรียนสำเร็จ"
      }
      ```

### 5.3 หลักสูตร (เพิ่ม user_id)
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/curriculums`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "curriculum_id": 1,
          "curriculum_name": "หลักสูตรเทคโนโลยีสารสนเทศ"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/curriculums/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "curriculum_id": 1,
        "curriculum_name": "หลักสูตรเทคโนโลยีสารสนเทศ",
        "user_id": 2
      }
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/curriculums`
  - **Request Body**:
    ```json
    [
      {
        "curriculum_name": "หลักสูตรใหม่",
        "year": 2025,
        "user_id": 2
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มหลักสูตรสำเร็จ",
        "curriculum_id": 6
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/curriculums`
  - **Request Body**:
    ```json
    [
      {
        "curriculum_id": 6,
        "curriculum_name": "หลักสูตรใหม่ (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขหลักสูตรสำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/curriculums/6`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบหลักสูตรสำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/curriculums`
  - **Request Body**:
    ```json
    [6]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบหลักสูตรสำเร็จ"
      }
      ```

### 5.4 แผนก
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/departments`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "department_id": 1,
          "department_name": "เทคโนโลยีสารสนเทศ"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/departments/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "department_id": 1,
        "department_name": "เทคโนโลยีสารสนเทศ"
      }
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/departments`
  - **Request Body**:
    ```json
    [
      {
        "department_name": "แผนกใหม่",
        "college_id": 1
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มแผนกสำเร็จ",
        "department_id": 2
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/departments`
  - **Request Body**:
    ```json
    [
      {
        "department_id": 2,
        "department_name": "แผนกใหม่ (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขแผนกสำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/departments/2`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบแผนกสำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/departments`
  - **Request Body**:
    ```json
    [2]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบแผนกสำเร็จ"
      }
      ```

### 5.5 กลุ่มผู้เรียน (เพิ่ม user_id)
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/groups`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "group_id": 1,
          "group_code": "IT01"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/groups/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "group_id": 1,
        "group_code": "IT01",
        "user_id": 1
      }
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/groups`
  - **Request Body**:
    ```json
    [
      {
        "group_code": "IT03",
        "level": "ปวช",
        "room_type": "ปกติ",
        "year": 2025,
        "student_count": 30,
        "department_id": 1,
        "curriculum_id": 1,
        "user_id": 2
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มกลุ่มสำเร็จ",
        "group_id": 6
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/groups`
  - **Request Body**:
    ```json
    [
      {
        "group_id": 6,
        "group_code": "IT03 (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขกลุ่มสำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/groups/6`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบกลุ่มสำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/groups`
  - **Request Body**:
    ```json
    [6]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบกลุ่มสำเร็จ"
      }
      ```

### 5.6 สิทธิ
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/roles`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "role_id": 1,
          "role_name": "admin"
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/roles/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "role_id": 1,
        "role_name": "admin"
      }
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/roles`
  - **Request Body**:
    ```json
    [
      {
        "role_name": "new_role"
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มสิทธิ์สำเร็จ",
        "role_id": 5
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/roles`
  - **Request Body**:
    ```json
    [
      {
        "role_id": 5,
        "role_name": "new_role (อัพเดท)"
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขสิทธิ์สำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/roles/5`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบสิทธิ์สำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/roles`
  - **Request Body**:
    ```json
    [5]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบสิทธิ์สำเร็จ"
      }
      ```

### 5.7 ภาคเรียน
- **ดูทั้งหมด**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/semesters`
  - **Response**: 
    - **200 OK**:
      ```json
      [
        {
          "semester_id": 1,
          "semester_number": 1
        }
      ]
      ```
- **ดูเฉพาะ ID**: 
  - **Method**: GET
  - **URI**: `http://127.0.0.1/plr/api/semesters/1`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "semester_id": 1,
        "semester_number": 1
      }
      ```
- **เพิ่ม (หลายรายการ)**: 
  - **Method**: POST
  - **URI**: `http://127.0.0.1/plr/api/semesters`
  - **Request Body**:
    ```json
    [
      {
        "semester_number": 2,
        "academic_year": 2025,
        "start_date": "2025-12-01",
        "weeks": 16
      }
    ]
    ```
  - **Response**: 
    - **201 Created**:
      ```json
      {
        "message": "เพิ่มภาคเรียนสำเร็จ",
        "semester_id": 2
      }
      ```
- **แก้ไข (หลายรายการ)**: 
  - **Method**: PUT
  - **URI**: `http://127.0.0.1/plr/api/semesters`
  - **Request Body**:
    ```json
    [
      {
        "semester_id": 2,
        "weeks": 18
      }
    ]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "แก้ไขภาคเรียนสำเร็จ"
      }
      ```
- **ลบ (เฉพาะ ID)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/semesters/2`
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบภาคเรียนสำเร็จ"
      }
      ```
- **ลบ (หลายรายการ)**: 
  - **Method**: DELETE
  - **URI**: `http://127.0.0.1/plr/api/semesters`
  - **Request Body**:
    ```json
    [2]
    ```
  - **Response**: 
    - **200 OK**:
      ```json
      {
        "message": "ลบภาคเรียนสำเร็จ"
      }
      ```

---

## 6. ระบบอัพโหลดไฟล์ภาพ
ทุกคนที่เข้าสู่ระบบสามารถใช้งานได้

### 6.1 อัพโหลดลายเซ็น
- **Method**: POST
- **URI**: `http://127.0.0.1/plr/api/users/upload-signature`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: [teacher|admin|department-head|director]
  Content-Type: multipart/form-data
  ```
- **Request Body**: ไฟล์ .png หรือ .jpg ขนาด < 8MB (ชื่อไฟล์: signature.png)
- **Response**:
  - **200 OK**:
    ```json
    {
      "message": "อัปโหลดลายเซ็นสำเร็จ",
      "signature_path": "/dashboard/assets/images/signatures/signature.png"
    }
    ```
  - **400 Bad Request**: ไฟล์ไม่ถูกต้อง

### 6.2 ลบลายเซ็น
- **Method**: DELETE
- **URI**: `http://127.0.0.1/plr/api/users/delete-signature`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: [teacher|admin|department-head|director]
  ```
- **Response**:
  - **200 OK**:
    ```json
    {
      "message": "ลบลายเซ็นสำเร็จ"
    }
    ```
  - **404 Not Found**: ไม่พบลายเซ็น

### 6.3 เปลี่ยนลายเซ็น
- **Method**: POST
- **URI**: `http://127.0.0.1/plr/api/users/update-signature`
- **Request Headers**:
  ```
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  X-Active-Role: [teacher|admin|department-head|director]
  Content-Type: multipart/form-data
  ```
- **Request Body**: ไฟล์ .png หรือ .jpg ขนาด < 8MB (ชื่อไฟล์: new_signature.png)
- **Response**:
  - **200 OK**:
    ```json
    {
      "message": "อัปเดตลายเซ็นสำเร็จ",
      "signature_path": "/dashboard/assets/images/signatures/new_signature.png"
    }
    ```
  - **400 Bad Request**: ไฟล์ไม่ถูกต้อง

---

## การใช้งานทั่วไป
- **Bearer Token**: ใส่ใน header `Authorization: Bearer <token>` หลังจากล็อกอิน
- **X-Active-Role**: ระบุบทบาท เช่น `X-Active-Role: teacher`
- **สถานะทั่วไป**:
  - **401 Unauthorized**: Token หมดอายุหรือไม่มีสิทธิ์
  - **403 Forbidden**: ไม่มีสิทธิ์เข้าถึง
  - **500 Internal Server Error**: ปัญหาด้านเซิร์ฟเวอร์