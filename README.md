# -n-t-t-nghi-p
ô kê la
import cv2
import time

# 1. Tải bộ nhận diện khuôn mặt
face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')

# 2. Mở camera
cap = cv2.VideoCapture(0)

print("Hệ thống tự động chụp đang chạy... Nhấn 'q' để dừng.")

last_taken_time = 0  # Biến để kiểm soát thời gian giữa các lần chụp (tránh chụp quá nhiều ảnh 1 lúc)
cooldown = 3  # Cứ sau 3 giây mới cho phép chụp tiếp nếu vẫn thấy mặt

while True:
    ret, frame = cap.read()
    if not ret:
        break

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, 1.1, 5)

    # 3. Nếu tìm thấy ít nhất 1 khuôn mặt
    if len(faces) > 0:
        current_time = time.time()

        # Kiểm tra xem đã qua thời gian chờ (cooldown) chưa
        if current_time - last_taken_time > cooldown:
            # Vẽ khung đỏ để báo hiệu đang chụp
            for (x, y, w, h) in faces:
                cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 0, 255), 3)

            # Tạo tên file theo thời gian để không bị trùng
            filename = f"auto_snap_{int(current_time)}.jpg"
            cv2.imwrite(filename, frame)

            print(f"Đã tự động chụp: {filename}")
            last_taken_time = current_time  # Cập nhật lại thời gian chụp cuối cùng

    # Vẽ khung xanh cho chế độ xem thử (preview)
    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)

    cv2.imshow('Tu dong chup khi thay mat', frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
