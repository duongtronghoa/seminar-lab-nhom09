# Báo cáo lab Nhóm 09

**Họ tên:** Dương Trọng Hòa · **MSSV:** 23120127 · **JDK đã dùng** (kết quả `java -version`): Java 25.0.2 LTS

## Bài 1: Giá trị biên

| Ngày trễ | Hạng | Số tiền bạn điền | Cách tính |
|---:|---|---:|---|
| 24 | NORMAL | 48.000đ | 24 × 2.000 = 48.000đ, chưa chạm mức trần. |
| 25 | NORMAL | 50.000đ | 25 × 2.000 = 50.000đ, vừa chạm mức trần. |
| 26 | NORMAL | 50.000đ | 26 × 2.000 = 52.000đ, giới hạn còn 50.000đ. |
| 2 | VIP | 0đ | Hai ngày đầu được ân hạn. |
| 3 | VIP | 1.000đ | (3 − 2) × 1.000 = 1.000đ. |
| 51 | VIP | 49.000đ | (51 − 2) × 1.000 = 49.000đ, chưa chạm mức trần. |
| 52 | VIP | 50.000đ | (52 − 2) × 1.000 = 50.000đ, vừa chạm mức trần. |
| 53 | VIP | 50.000đ | (53 − 2) × 1.000 = 51.000đ, giới hạn còn 50.000đ. |

**Vì sao chọn các ngày này mà không chọn ngày 10?**
Các ngày 24, 25, 26 kiểm tra ngay trước, tại và sau mức trần của NORMAL. Ngày 2 và 3 kiểm tra ranh giới ân hạn của VIP; 51, 52, 53 kiểm tra ngay trước, tại và sau mức trần của VIP. Ngày 10 nằm giữa các khoảng, ít khả năng phát hiện lỗi sai dấu so sánh hoặc sai một ngày ở ranh giới.

## Bài 2a: Coverage

| | Branch coverage của `LoanService` |
|---|---|
| Trước khi viết test | 0,66 (66%) |
| Sau khi viết 2 test | 1,00 (100%) |

**Test `daDatHanMuc` của bạn dùng `countActiveByMemberId` trả về giá trị nào? Vì sao chọn giá trị đó?**
Ban đầu dùng 4 để chạy qua nhánh từ chối của mã đang có và đạt coverage 100%. PIT cho thấy test này chưa kiểm tra ranh giới. Tôi đổi giá trị trả về thành **3** vì đặc tả cho phép mượn tối đa 3 cuốn, nên người đang mượn đúng 3 cuốn không được mượn thêm. Test mới đỏ với mã cũ và xanh sau khi sửa lỗi.

## Bài 2b: Mutation testing

**Lần chạy PIT đầu tiên:** có bao nhiêu mutant sống sót (SURVIVED)? Nằm ở dòng nào, là loại mutant gì?
Có **1/9 mutant SURVIVED** tại dòng 28 của `LoanService.java`: `ConditionalsBoundaryMutator` đổi ranh giới của điều kiện kiểm tra số sách đang mượn (`> 3` thành `>= 3`).

**Lỗi cài sẵn nằm ở đâu? Bạn phát hiện ra nó bằng cách nào?**
Lỗi nằm trong `LoanService.borrow`: điều kiện `countActiveByMemberId(memberId) > 3` vẫn cho mượn thêm khi thành viên đang giữ đúng 3 cuốn, trái với đặc tả. Tôi phát hiện qua mutant ranh giới còn sống, đối chiếu đặc tả, rồi chạy test với giá trị 3 để xác nhận test thất bại trên mã cũ.

**Bạn đã sửa test và code như thế nào?**
Trong `daDatHanMuc`, tôi cho `countActiveByMemberId(1)` trả về `3L`, kiểm tra mã lỗi `LIMIT_REACHED` và xác nhận không lưu phiếu mượn hay trạng thái sách. Trong `LoanService.borrow`, tôi đổi `> 3` thành `>= 3`. Test `conPhiChuaTra` kiểm tra mã `UNPAID_FINE` khi phí nợ là 1.000đ và xác nhận không có thao tác lưu.

**Kết quả PIT sau khi sửa** (Killed bao nhiêu / tổng bao nhiêu):
**Killed 9/9**, **Survived 0**, **No coverage 0**. `clean verify` cũng đạt branch coverage 100% và `BUILD SUCCESS`.

## Câu hỏi tổng kết
**Coverage 100% có nghĩa là code không còn lỗi không? Hãy giải thích dựa trên những gì bạn thấy trong lab.**
Không. Trước khi sửa lỗi, `LoanService` đã có branch coverage 100% và build xanh, nhưng vẫn cho mượn cuốn thứ tư khi thành viên đang giữ đúng 3 cuốn. Coverage chỉ chứng minh các nhánh đã chạy; test cần khẳng định kết quả đúng tại ranh giới. PIT làm lộ khoảng trống đó bằng mutant đổi dấu so sánh còn sống.
