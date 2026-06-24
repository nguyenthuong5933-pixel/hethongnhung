```mermaid
graph TD
    %% Cụm 1: Khởi tạo và Tiếp nhận mã UID
    Start() --> Init[Khởi tạo hệ thống: Kết nối Firestore & NfcAdapter]
    
    Init --> CheckWait{Chờ thao tác lấy UID}
    
    CheckWait -->|Cách 1: NFC| ScanNFC[/Quét thẻ NFC/]
    ScanNFC --> SaveUID1
    SaveUID1 --> AssignUID
    
    CheckWait -->|Cách 2: Nhập tay| InputUID
    InputUID --> AssignUID
    
    CheckWait -->|Cách 3: Auto Mode| AutoMode{Bật công tắc isAutoMode?}
    AutoMode -->|Đúng| ListenUID
    ListenUID --> ReceiveUID
    ReceiveUID --> AssignUID
    
    %% Cụm 2: Xác thực Bệnh nhân (Routing)
    AssignUID --> CheckUIDEmpty{Biến currentUidState rỗng?}
    CheckUIDEmpty -->|Rỗng| ShowWaitScreen
    ShowWaitScreen --> CheckWait
    
    CheckUIDEmpty -->|Có dữ liệu| QueryDB
    QueryDB --> CheckExist{Bệnh nhân có tồn tại?}
    
    CheckExist -->|Không| ShowReg
    ShowReg --> InputInfo[/Nhập thông tin bệnh nhân/]
    InputInfo --> SaveNew[Lưu hồ sơ mới lên Firebase]
    SaveNew -->|Thành công| CheckExist
    
    %% Cụm 3: Giám sát Thời gian thực và Cảnh báo
    CheckExist -->|Có| ShowMedicalApp
    ShowMedicalApp --> StartListenVitals
    StartListenVitals --> ReceiveVitals
    
    ReceiveVitals --> CheckAlert{Cảnh báo: Nhịp tim bất thường HOẶC SpO2 < 95?}
    
    CheckAlert -->|Đúng| TriggerAlert
    CheckAlert -->|Sai| NormalState
    
    TriggerAlert --> CheckCloseAction
    NormalState --> CheckCloseAction
    
    CheckCloseAction{Nhấn nút ĐÓNG HỒ SƠ?}
    CheckCloseAction -->|Không| ReceiveVitals
    
    CheckCloseAction -->|Có| CloseProfile
    CloseProfile --> CheckUIDEmpty
```
