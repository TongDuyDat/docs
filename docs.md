# Biểu diễn toán học của class WrongWayTracker

Dưới đây là biểu diễn toán học cho logic của class `WrongWayTracker` trong hàm `update()`.

## Định nghĩa các biến
- $t$: Thời gian hiện tại ($t = \text{time.time()}$).
- $V_{id}$: ID của xe (`vehicle_id`).
- $B = [x_1, y_1, x_2, y_2]$: Bounding box hiện tại của xe.
- $H_{id}$: Lịch sử vị trí của xe $V_{id}$, là một danh sách các cặp $(B_i, t_i)$, với $B_i$ là bounding box tại thời điểm $t_i$.
- $n_{min} = 10$: Số lượng tối thiểu các điểm lịch sử cần thiết (`self.minimun_history`).
- $\tau = 15.0$: Ngưỡng chuyển động dọc tối thiểu (`self.threshold`).
- $d_{road}$: Hướng đường hợp lệ (`self.road_direction`, "bottom_to_top" hoặc "top_to_bottom").
- $e_{max} = 10$: Số lỗi cho phép tối đa (`self.max_permissible_error`).
- $e_{id}$: Số lỗi hiện tại của xe $V_{id}$ (`vehicle_data["permissible_error"]`).

## Hàm phụ
1. **Tính tọa độ y của tâm đáy bounding box**:
   $$
   y_{bottom} = \frac{y_1 + y_2}{2}
   $$
   Trong đó:
   - $y_{current} = \frac{y_1 + y_2}{2}$ (tọa độ y hiện tại từ $B$).
   - $y_{prev} = \frac{y_{1,prev} + y_{2,prev}}{2}$ (tọa độ y từ $B_{prev}$ trong lịch sử).

2. **Tính độ chênh lệch y**:
   $$
   \Delta y = y_{current} - y_{prev}
   $$
   Với $y_{prev}$ được lấy từ $H_{id}[-n_{min}]$, tức là điểm cách hiện tại $n_{min}$ bước trong lịch sử.

## Logic toán học của hàm update()
### Bước 1: Kiểm tra lịch sử
- Nếu $|H_{id}| < n_{min}$ (số lượng điểm lịch sử < 10):
  $$
  is_{wrong_way} = \text{None}
  $$
  Kết quả:
  $$
  f(V_{id}, B) = \{ V_{id}, is_{wrong_way} = \text{None} \}
  $$

### Bước 2: Kiểm tra ngưỡng chuyển động
- Nếu $|\Delta y| \leq \tau$ (chênh lệch y nhỏ hơn ngưỡng):
  $$
  is_{wrong_way} = \text{False}
  $$
  Kết quả:
  $$
  f(V_{id}, B) = \{ V_{id}, is_{wrong_way} = \text{False} \}
  $$

### Bước 3: Xác định hướng sai
- Định nghĩa điều kiện sai hướng:
  $$
  is_{wrong_way_raw} =
  \begin{cases} 
  \Delta y < 0 & \text{nếu } d_{road} = "bottom_to_top" \\
  \Delta y > 0 & \text{nếu } d_{road} = "top_to_bottom"
  \end{cases}
  $$

### Bước 4: Xử lý lỗi cho phép
- Nếu $is_{wrong_way_raw} = \text{True}$ và $e_{id} < e_{max}$:
  $$
  e_{id} \leftarrow e_{id} + 1
  $$
  $$
  H_{id}[-n_{min} + 1] \leftarrow (B, t)
  $$
  $$
  is_{wrong_way} = \text{False}
  $$
- Ngược lại:
  $$
  e_{id} \leftarrow 0
  $$
  $$
  is_{wrong_way} = is_{wrong_way_raw}
  $$

### Kết quả cuối cùng
- Hàm trả về:
  $$
  f(V_{id}, B) = \{ V_{id}, is_{wrong_way} \}
  $$

## Tổng quát hóa
Hàm có thể được diễn đạt dưới dạng toán học như sau:
$$
f(V_{id}, B) =
\begin{cases} 
\{ V_{id}, \text{None} \} & \text{nếu } |H_{id}| < n_{min} \\
\{ V_{id}, \text{False} \} & \text{nếu } |\Delta y| \leq \tau \\
\{ V_{id}, \text{False} \} & \text{nếu } is_{wrong_way_raw} = \text{True} \land e_{id} < e_{max} \\
\{ V_{id}, is_{wrong_way_raw} \} & \text{ngược lại}
\end{cases}
$$

## Ghi chú
- $is_{wrong_way_raw}$ là kết quả kiểm tra hướng dựa trên $\Delta y$ và $d_{road}$.
- Việc cập nhật $e_{id}$ và $H_{id}$ chỉ xảy ra trong trường hợp có lỗi sai hướng nhưng vẫn trong ngưỡng cho phép.
