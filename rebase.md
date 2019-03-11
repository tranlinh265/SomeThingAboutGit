# git rebase
Đề cập đến khái niệm và cách sử dụng rebase.

Rebase là 1 trong 2 tiện ích của Git, chuyên được sử dụng để tích hợp thay đổi giữa các branch. Tiện ích còn lại là Merge. Mỗi tiện ích đều có điểm mạnh và hạn chế riêng, sẽ được tổng hợp trong 1 bài viết khác :D

## ví dụ sử dụng

``` 
     A1-A2-A3						(branch feature_A)
    /
M0-M1-M2-M3							(branch master)
          \
          B1-B2-B3 					(branch feature_B)
```

## git rebase là gì?
```
Rebase says I want the point at which I branched to move to a new starting point
```

master là branch chính, khi thực hiện các công việc khác nhau, ta tạo ra các branch feature. Base của branch là 1 commit mà đánh dấu sự chuyển hướng branch, so với branch gốc.
Như ví dụ trên, M1 là base của branch feature_A, M3 là base của branch feature_B.

Vậy có thể hiểu nôm na, quá trình rebase là việc ta thay đổi vị trí base của branch từ 1 commit sang 1 commit khác. Đồng nghĩa với việc thay đổi điểm gốc tạo ra branch. 
Quay lại ví dụ trên, thực hiện rebase feature_A vào master sẽ thay đổi base của feature_A từ commit M1 sang commit M3. (1)
``` 
      (master)   (feature_A)
          |          |
M0-M1-M2-M3-A1'-A2'-A3'							
          \
          B1-B2-B3					(branch feature_B)
```
Thực hiện rebase feature_B vào master sẽ k có gì thay đổi vì base của feature_B vốn đã ở commit M3.
``` 
     A1-A2-A3						(branch feature_A)
    /
M0-M1-M2-M3-B1-B2-B3						
         |        |
     (master) (feature_B)
```
Nhưng nếu thực hiện rebase feature_A vào master xog rebase feature_B vào feature_A thì sao ?
```
      (master)   (feature_A)
          |          |
M0-M1-M2-M3-A1'-A2'-A3'-B1'-B2'-B3'	
                                 |						
                            (feature_B)
```
Quá trình rebase được thực hiện trên 1 branch trung gian, sau khi kết thúc rebase, kết quả của branch trung gian này được apply vào branch gốc.

## Tại sao lại có commit A1',A2',B1',B2'...
Thực tế, trong quá trình rebase sẽ gặp conflict. Như ở (1), đẩy commit của master vào branch trung gian, sau đó apply từng commit của feature_A vào branch trung gian. Dẫn đến việc, mỗi khi aplly 1 commit, nếu có conflict, sẽ tạo ra 1 commit mới, thay thế commit cũ, 1 phần nội dung của commit cũ, và 1 phần nội dung sửa conflict.

Đây chính là điểm khác biệt so với sử dụng merge. Merge lấy toàn bộ thay đổi trên 1 branch, so sánh với branch khác và nhét toàn bộ sự thay đổi vào trong 1 commit trên nhánh đích. Còn rebase không tạo ra commit tổng hợp, tất cả được gửi gắm vào các commit con.
