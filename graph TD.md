graph TD;
    %% External Entities
    Admin[System Administrator]
    Analyst[Cybersecurity Analyst]
    SourceSystems[Source Systems]
    NotificationChannels[Notification Channels]

    %% Data Stores
    D1[D1: Knowledge Base<br>(Vector DB)]
    D2[D2: Processed Logs<br>(Log Storage)]
    D3[D3: User Accounts]
    D4[D4: Alerting Rules]

    %% Main Processes
    subgraph Advance agent for Cybersecurity
        P1(1.0<br>จัดการองค์ความรู้);
        P2(2.0<br>รวบรวมและ<br>ประมวลผล Log);
        P3(3.0<br>สืบค้นและ<br>วิเคราะห์เหตุการณ์);
        P4(4.0<br>ยืนยันตัวตนผู้ใช้);
        P5(5.0<br>ส่งออกรายงาน);
        P6(6.0<br>จัดการและส่ง<br>การแจ้งเตือน);
    end

    %% Flows for Admin
    Admin -- "1.1 นำเข้าเอกสาร (PDF/DOCX)<br>1.3 ลบองค์ความรู้" --> P1
    P1 -- "สถานะการประมวลผล" --> Admin
    Admin -- "ข้อมูลเข้าระบบ" --> P4

    %% Flows for Analyst
    Analyst -- "4.1 ข้อมูลเข้าระบบ/ออกจากระบบ" --> P4
    P4 -- "Session ที่ผ่านการยืนยัน" --> Analyst

    Analyst -- "3.1 คำค้นหาภาษาธรรมชาติ<br>3.3 ขยายผลการสืบค้น" --> P3;
    P3 -- "3.2 ผลลัพธ์การวิเคราะห์" --> Analyst
    
    Analyst -- "5.1 คำขอสร้างรายงาน" --> P5
    P5 -- "5.2, 5.3 รายงาน (CSV/PDF)" --> Analyst

    Analyst -- "6.1 กำหนดกฎการแจ้งเตือน" --> P6

    %% Flows for Source Systems
    SourceSystems -- "2.1 ข้อมูล Syslog" --> P2

    %% Flows for Notification Channels;
    P6 -- "6.3 ข้อความแจ้งเตือน" --> NotificationChannels

    %% Internal Flows between Processes and Data Stores
    P1 -- "1.2 Vector Embedding" --> D1;
    P1 -- "อ่าน/ลบข้อมูล" --> D1;
    
    P2 -- "2.2 ข้อมูล Log ที่จัดโครงสร้างแล้ว<br>2.3 จัดเก็บ Log" --> D2;
    
    P3 -- "ดึงข้อมูล Log ที่เกี่ยวข้อง" --> D2;
    D2 -- "ผลลัพธ์ Log" --> P3;
    P3 -- "ดึงองค์ความรู้ประกอบการวิเคราะห์" --> D1;
    D1 -- "ข้อมูลองค์ความรู้" --> P3;

    P3 -- "ข้อมูลผลลัพธ์เพื่อสร้างรายงาน" --> P5;
    
    P4 -- "ตรวจสอบสิทธิ์" --> D3;
    D3 -- "ข้อมูลสิทธิ์ผู้ใช้" --> P4;

    P2 -- "ข้อมูล Log ที่เข้ามาใหม่" --> P6;
    P6 -- "บันทึก/อ่านกฎ" --> D4;
    D4 -- "เงื่อนไขการแจ้งเตือน" --> P6;