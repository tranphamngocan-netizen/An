graph TD
    %% === PHONG CÁCH ĐỒ HỌA ===
    classDef start_end fill:#f9f,stroke:#333,stroke-width:2px,rx:20,ry:20;
    classDef process fill:#bbf,stroke:#333,stroke-width:1px;
    classDef condition fill:#ff9,stroke:#333,stroke-width:1px;
    classDef sub fill:#fbf,stroke:#333,stroke-width:1px;
    classDef io fill:#fbc,stroke:#333,stroke-width:1px;

    %% === KHỞI ĐỘNG ===
    Start([Bắt đầu Cài đặt Dữ liệu Hệ thống]) :::start_end --> Login[Đăng nhập: Nhập URL, Tên đăng nhập, Mật khẩu] :::process
    Login --> CheckLogin{Thông tin đúng?} :::condition
    CheckLogin -- Sai --> Login
    CheckLogin -- Đúng --> Menu[Màn hình chính: Truy cập các Menu chức năng] :::process

    %% === BƯỚC 1: ĐƠN VỊ ===
    Menu --> B1[BƯỚC 1: Tạo Đơn vị quản lý] :::process
    B1 --> B1_Add[Nhấn 'Thêm mới' Đơn vị] :::process
    B1_Add --> B1_Check{Nhập đủ Tên đơn vị & \nĐúng định dạng SĐT/Website?} :::condition
    B1_Check -- Sai --> B1_Add
    B1_Check -- Đúng --> B1_Save[Lưu: Hệ thống tự sinh mã DVxxxxx] :::io
    B1_Save --> B1_Rule{Hành động Chỉnh sửa / Xóa?} :::condition
    B1_Rule -- Chỉnh sửa --> B1_Edit[Không cho phép sửa Mã đơn vị] :::process
    B1_Rule -- Xóa --> B1_Del{Đơn vị đã gán cho Công trình?} :::condition
    B1_Del -- Đúng --> B1_Err[BÁO LỖI: Ràng buộc dữ liệu - Khóa xóa] :::process
    B1_Del -- Chưa gán --> B1_Confirm[Xóa thành công khỏi hệ thống] :::process

    %% === BƯỚC 2: DANH MỤC ===
    B1_Confirm & B1_Edit & B1_Save --> B2[BƯỚC 2: Tạo Danh mục dùng chung] :::process
    B2 --> B2_Add[Thêm mới Danh mục gốc \n Mã duy nhất: DEVICE, INFRASTRUCTURE, CAMERA] :::process
    B2_Add --> B2_Sub[Nhấn 'Thêm dữ liệu' vào danh mục con \n VD: Khai báo Cầu, Hầm, Tuyến, Dòng Camera] :::process
    B2_Sub --> B2_Rule{Yêu cầu Xóa danh mục?} :::condition
    B2_Rule -- Dữ liệu đang được sử dụng --> B2_Err[BÁO LỖI: Không cho phép xóa] :::process
    B2_Rule -- Chưa sử dụng/Trống --> B2_Del[Xóa thành công] :::process

    %% === BƯỚC 3 & 4: CẤU HÌNH LOẠI ===
    B2_Sub & B2_Del --> Branch_Config{Phân nhánh Cấu hình Loại} :::condition
    
    %% Bước 3: Loại công trình
    Branch_Config -->|Cấu hình Loại Công trình| B3[BƯỚC 3: Loại công trình] :::process
    B3 --> B3_Add[Chọn Nhóm công trình từ B2 \n Nhập Mã, Tên, Tải lên Biểu tượng] :::process
    B3_Add --> B3_Prop[Thêm các thuộc tính động: Chiều dài, Vật liệu...] :::process
    
    %% Bước 4: Loại thiết bị
    Branch_Config -->|Cấu hình Loại Thiết bị| B4[BƯỚC 4: Loại thiết bị] :::process
    B4 --> B4_Add[Chọn Nhóm thiết bị từ B2 \n Nhập Mã, Tên, Chọn Nhà sản xuất] :::process
    B4_Add --> B4_Cam{Thiết bị là Camera?} :::condition
    B4_Cam -- Đúng --> B4_Flag[Bắt buộc thêm 2 thuộc tính: \n is_parent và is_sync kiểu Đúng/Sai] :::process
    B4_Cam -- Sai --> B4_Prop[Thêm các trường thuộc tính kỹ thuật khác] :::process

    %% === BƯỚC 5: VỊ TRÍ ===
    B3_Prop & B4_Prop & B4_Flag --> B5[BƯỚC 5: Tạo danh sách Vị trí] :::process
    B5 --> B5_Add[Nhấn 'Thêm mới' Vị trí] :::process
    B5_Add --> B5_Check{Loại vị trí là \n 'Vị trí thiết bị' hoặc 'Vị trí công trình'?} :::condition
    B5_Check -- Sai --> B5_Err[Lưu ý: Vị trí sẽ không hiển thị trên Bản đồ] :::process
    B5_Check -- Đúng --> B5_Func[Chọn đúng Chức năng nghiệp vụ \n Giám sát sự kiện/Vi phạm/Lưu lượng/Biển số] :::process
    B5_Func --> B5_Save[Lưu thông tin Vị trí số] :::io
    B5_Save --> B5_Warn{! CẢNH BÁO: Nếu vị trí đã có dữ liệu nhận diện \n Tuyệt đối KHÔNG XÓA -> Chuyển sang 'Ngừng sử dụng'} :::process

    %% === BƯỚC 6: NỀN BẢN ĐỒ ===
    B5_Warn --> B6[BƯỚC 6: Cấu hình Nền bản đồ] :::process
    B6 --> B6_Add[Nhập Tên, Ảnh hiển thị, Link nền GeoServer, Layer WMS] :::process
    B6_Add --> B6_Geo[Nhập tọa độ Bounding Box & Điểm trung tâm bản đồ] :::process

    %% === BƯỚC 7: TẠO CÔNG TRÌNH CỤ THỂ ===
    B6_Geo --> B7[BƯỚC 7: Tạo Công trình cụ thể] :::process
    B7 --> B7_Add[Chọn Loại công trình từ B3 \n Gán Đơn vị quản lý từ B1] :::process
    B7_Add --> B7_Map[Chọn Điểm đầu & Điểm cuối từ danh sách Vị trí B5] :::process
    B7_Map --> B7_Save[Lưu: Công trình tự động hiển thị lên nền bản đồ số] :::io

    %% === BƯỚC 8: TẠO THIẾT BỊ CHI TIẾT ===
    B7_Save --> B8[BƯỚC 8: Tạo Thiết bị chi tiết] :::process
    B8 --> B8_Mode{Chọn phương thức tạo data} :::condition
    
    %% Cách 1: Thủ công
    B8_Mode -->|Thủ công| B8_Man[Nhấn 'Thêm mới' \n Nhập Serial, Tên, Chọn Loại từ B4] :::process
    B8_Man --> B8_Assign[Gán Thiết bị vào Vị trí B5 hoặc Công trình B7] :::process
    
    %% Cách 2: Excel
    B8_Mode -->|Nhập hàng loạt - Import| B8_Xls[Tải File Excel Mẫu hệ thống] :::process
    B8_Xls --> B8_Fill[Điền thông tin chuẩn định dạng mẫu \n Lấy id Công trình & site_id Vị trí] :::io
    B8_Fill --> B8_Up[Tải file .xls/.xlsx lên hệ thống] :::process
    B8_Up --> B8_Validate{Hệ thống kiểm tra dữ liệu?} :::condition
    B8_Validate -- Phát hiện lỗi --> B8_Export[Xuất thông báo dòng lỗi -> Sửa lại] :::io --> B8_Fill
    B8_Validate -- Hợp lệ --> B8_Import[Import thành công vào cơ sở dữ liệu] :::process

    %% Luồng rẽ nhánh Camera sang CCTV Milestone
    B8_Assign & B8_Import --> B8_CheckCam{Thiết bị là Camera \n và kích hoạt dùng tính năng?} :::condition
    B8_CheckCam -- Đúng --> B9[BƯỚC 9: Đồng bộ thiết bị sang CCTV] :::sub
    B9 --> B9_Sync[Mở App CCTV -> Nhấn 'Thêm Asset' \n Nhập ID từ Milestone, nhập Đường dẫn luồng LIVE/RTSP] :::process
    B8_CheckCam -- Sai --> B10_Checklist

    %% === BƯỚC 10: KIỂM TRA & KẾT THÚC ===
    B9_Sync --> B10_Checklist[BƯỚC 10: Checklist kiểm tra sau khi Setup] :::process
    B10_Checklist --> B10_Verify{Tất cả các hạng mục đạt yêu cầu? \n Đơn vị, Danh mục, Vị trí, Tài sản...} :::condition
    B10_Verify -- Chưa đạt --> Menu
    B10_Verify -- Đầy đủ & Chính xác --> End([Hệ thống sẵn sàng vận hành chính thức]) :::start_end
