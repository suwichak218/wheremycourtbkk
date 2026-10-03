# รายงานโครงการ Phase 2: Initial Design and Prototype
**โครงการ:** เว็บแอปพลิเคชันค้นหาและคัดกรองสนามบาสเกตบอลในกรุงเทพมหานคร (wheremycourtbkk)

---

## รายชื่อสมาชิกกลุ่ม
1. นายจตุรภัทร วงษ์ชื่น รหัสนิสิต 68102010193
2. นายฐานพัฒน์ อังศุโภไคย รหัสนิสิต 68102010197
3. นายสุวิจักขณ์ วงค์น้อย รหัสนิสิต 68102010218
4. นายอคิราห์ ไทโย เลิศวิทยา รหัสนิสิต 68102010219

---

## 1. ข้อมูลเดิมจาก Phase 1 (Original Requirements)

### 1.1 Functional Requirements (FR)
-**FR1:** ระบบค้นหาพิกัดสนามตามตำแหน่งปัจจุบัน (GPS) และค้นหาตามย่าน/ทำเล

-**FR2:** ระบบคัดกรองตามราคา ประเภทสนาม เวลาเปิด-ปิด และระยะห่างรถไฟฟ้า BTS/MRT

-**FR3:** ระบบแสดงหมุดพิกัดบน Google Maps และนำทาง

-**FR4:** ระบบแสดงสิ่งอำนวยความสะดวก (ไฟ, แป้น, ที่จอดรถ, น้ำดื่ม, ห้องน้ำ)

-**FR5:** ระบบรายงานสถานะสด (Live Status)

-**FR6:** ระบบบอร์ดหาตี้และหารค่าคอร์ท

-**FR7:** ระบบรีวิวและอัปโหลดภาพสภาพสนาม

### 1.2 Non-Functional Requirements (NFR)
-**NFR1 (Performance):** หน้าเว็บและแผนที่ต้องโหลดแสดงผลเสร็จสิ้นภายใน 3 วินาที

-**NFR2 (Usability):** รองรับ Responsive Design บนมือถือ แท็บเล็ต และคอมพิวเตอร์

-**NFR3 (Reliability):** ใช้งานแผนที่ได้ต่อเนื่อง 99% ผ่าน Google Maps API

---

## 2. สิ่งที่เปลี่ยนแปลงจากรายงาน Phase 1 และเหตุผลที่เปลี่ยน

* **สิ่งที่เปลี่ยนแปลง:** 
  * []
* **เหตุผลที่เปลี่ยน:** 
  * []

---

## 3. Design Document

### 3.1 Architectural Design (High-Level Architecture)
```mermaid
flowchart TB
    %% ระดับผู้ใช้งานและอุปกรณ์
    subgraph Client_Layer ["1. เลเยอร์ฝั่งผู้ใช้งาน (Client Layer)"]
        User["🏀 ผู้ใช้งานทั่วไป / สมาชิก"]
        Browser["🌐 เว็บเบราว์เซอร์ (Responsive Web)"]
        User --> Browser
    end

    %% ระดับหน้าบ้าน / ส่วนแสดงผล
    subgraph Frontend_Layer ["2. เลเยอร์ส่วนหน้าบ้าน (Frontend Layer)"]
        UI["💻 เว็บแอปพลิเคชัน"]
        subgraph UI_Modules ["หน้าจอและโมดูลแสดงผล"]
            SearchPage["🔍 ค้นหาและตัวกรองสนาม"]
            MapComponent["🗺️ แผนที่พิกัดสนาม (Google Maps)"]
            CourtDetail["📋 รายละเอียดสนามและสิ่งอำนวยความสะดวก"]
            LiveFeed["⚡ รายงานสถานะสนามสด (Live Status)"]
            MatchBoard["🤝 กระดานจับคู่ / หาตี้หารค่าคอร์ท"]
            ReviewSection["⭐ ระบบรีวิวและแกลเลอรีรูปภาพ"]
            AuthModule["🔐 เข้าสู่ระบบ / ข้อมูลโปรไฟล์"]
        end
        Browser --> UI
        UI --> SearchPage & MapComponent & CourtDetail & LiveFeed & MatchBoard & ReviewSection & AuthModule
    end

    %% ระดับประมวลผล / หลังบ้าน
    subgraph Backend_Layer ["3. เลเยอร์หลังบ้านและ API (Backend Layer)"]
        APIGateway["🚪 ช่องทางรับส่งข้อมูลหลัก (API Gateway / Router)"]
        
        subgraph Services ["ระบบประมวลผลและบริการต่าง ๆ (Services)"]
            AuthService["ระบบจัดการผู้ใช้และสิทธิ์"]
            CourtService["ระบบข้อมูลสนามและสิ่งอำนวยความสะดวก"]
            SearchService["ระบบค้นหาตามระยะทางและคัดกรอง"]
            LiveStatusService["ระบบบันทึกสถานะสดของสนาม"]
            MatchService["ระบบบอร์ดจับคู่ผู้เล่น"]
            ReviewService["ระบบรีวิวและให้คะแนนดาว"]
            MediaService["ระบบจัดการอัปโหลดไฟล์รูปภาพ"]
        end

        UI_Modules -->|HTTPS / REST API| APIGateway
        APIGateway --> AuthService
        APIGateway --> CourtService
        APIGateway --> SearchService
        APIGateway --> LiveStatusService
        APIGateway --> MatchService
        APIGateway --> ReviewService
        APIGateway --> MediaService
    end

    %% ระดับฐานข้อมูลและที่จัดเก็บไฟล์
    subgraph Data_Layer ["4. เลเยอร์จัดเก็บข้อมูล (Data Persistence Layer)"]
        DB[("🗄️ ฐานข้อมูลหลัก Database")]
        
        subgraph DBSchema ["ตารางข้อมูล"]
            T_Users["ข้อมูลผู้ใช้งาน (Users)"]
            T_Courts["ข้อมูลสนามบาส (Courts)"]
            T_Amenities["สิ่งอำนวยความสะดวก (Facilities)"]
            T_LiveStatus["ประวัติรายงานสถานะสด (Live Status)"]
            T_Matchmaking["โพสต์หาเพื่อนเล่นบาส (Matchmaking)"]
            T_Reviews["ข้อมูลรีวิวและคะแนน (Reviews)"]
        end

        CloudStorage[("☁️ พื้นที่เก็บรูปภาพบนคลาวด์<br/>(Cloud Object Storage)")]

        AuthService --> T_Users
        CourtService --> T_Courts
        CourtService --> T_Amenities
        SearchService --> T_Courts
        LiveStatusService --> T_LiveStatus
        MatchService --> T_Matchmaking
        ReviewService --> T_Reviews
        MediaService -->|บันทึกไฟล์รูปภาพ| CloudStorage
        MediaService -->|บันทึกลิงก์ URL รูปภาพ| T_Reviews
    end

    %% บริการภายนอก
    subgraph External_APIs ["5. บริการภายนอก (External APIs)"]
        GoogleMaps["📍 Google Maps JavaScript API"]
        GoogleGeo["🧭 Geolocation API / บริการเส้นทางนำทาง"]
    end

    %% การเชื่อมต่อบริการภายนอก
    MapComponent <-->|แสดงแผนที่และหมุดพิกัด| GoogleMaps
    SearchService <-->|คำนวณพิกัดและระยะทางรถไฟฟ้า| GoogleGeo
```

### 3.2 Use Case Diagram
```mermaid
flowchart LR
    %% Actors
    subgraph Actors ["ผู้ใช้งานระบบ Actors"]
        direction TB
        GU["ผู้ใช้งานทั่วไป\nGeneral User"]
        RM["สมาชิกที่ลงทะเบียน\nRegistered Member"]
        GU -.->|ขยายสิทธิ์การใช้งาน| RM
    end

    %% System Boundary
    subgraph WheremycourtBKK ["ระบบเว็บแอปพลิเคชัน wheremycourtbkk"]
        direction TB

        %% Core / Public Use Cases
        subgraph Public_Features ["ฟังก์ชันทั่วไป Public Features"]
            UC1["UC-01: ค้นหาสนามตามพิกัด / ย่าน GPS & Search"]
            UC2["UC-02: คัดกรองสนาม Filter by Price, Surface, BTS/MRT"]
            UC3["UC-03: ดูแผนที่และเปิดเส้นทางนำทาง Interactive Map"]
            UC4["UC-04: ดูรายละเอียดและสิ่งอำนวยความสะดวก Court Details"]
            UC5["UC-05: ดูคะแนนรีวิวและภาพถ่ายสภาพสนามจริง"]
            UC6["UC-06: ดูสถานะความเคลื่อนไหวสนามสด Live Status"]
            UC7["UC-07: ดูกระดานประกาศหาตี้ / ชวนเล่นบาส"]
            UC8["UC-08: สมัครสมาชิกและเข้าสู่ระบบ Sign Up / Sign In"]
        end

        %% Member Only Use Cases
        subgraph Member_Features ["ฟังก์ชันเฉพาะสมาชิก Member-only Features"]
            UC9["UC-09: รายงานสถานะสนามสด Report Live Crowd / Court Status"]
            UC10["UC-10: โพสต์สร้างตี้ / ประกาศหารค่าเช่าสนาม"]
            UC11["UC-11: เขียนรีวิว ให้คะแนนดาว และอัปโหลดรูปสนามจริง"]
            UC12["UC-12: จัดการโปรไฟล์ส่วนตัว Manage Profile"]
        end
    end

    %% Connections for General User
    GU --> UC1
    GU --> UC2
    GU --> UC3
    GU --> UC4
    GU --> UC5
    GU --> UC6
    GU --> UC7
    GU --> UC8

    %% Connections for Registered Member
    RM --> UC9
    RM --> UC10
    RM --> UC11
    RM --> UC12
```

## 4. UI Design & Prototype Screenshots

### 4.1 Figma Prototype Link & Overview
**ลิงก์ Figma Design:** https://www.figma.com/design/V7rcTsqLeaMGApU4bt7l21/WhereMyCourtBKKFinal?node-id=21-6477&t=6iatY9JYgsdvJKOt-1

### 4.2 Website Screenshots
**หน้า 1:**

**หน้า 2:**

## 5. กระบวนการทำงาน (Process, Methods, and Tools ที่เพิ่มเติมจาก Phase 1)
**การติดตามสถานะงาน (Project Tracking):** ใช้ GitHub Projects ในรูปแบบ Kanban Board แบ่งสถานะงานเป็น Todo, In Progress, Done

**ความถี่ของการประชุม (Scrum Cadence):** จัดประชุม Standup ประจำสัปดาห์สัปดาห์ละ 1-2 ครั้ง ผ่าน Discord

**ข้อกำหนดการตั้งชื่อ Branch (Branching Convention):**

**รูปแบบ Commit Message (Commit Formatting):**

**เครื่องมือและการสื่อสาร (Communication Tools):**
-**Discord:** ใช้สำหรับการประชุมอัปเดตงาน ประชุม Retrospective และแชร์หน้าจอเขียนโค้ด
-**LINE Group**: ใช้สำหรับประสานงานและแจ้งเตือน

## 6. สรุปการประชุมทบทวนการทำงาน (Sprint Retrospective)
**ลิงก์วิดีโอ Retrospective (YouTube):**

### 6.1 สิ่งที่ทำได้ดี (What went well)

### 6.2 ปัญหาและอุปสรรคที่พบ (What could be improved)

### 6.3 แนวทางการปรับปรุงใน Sprint ถัดไป (Action Items)
