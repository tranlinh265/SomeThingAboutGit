# Muốn sửa commit thì làm gì?

Có nhiều trường hợp có thể xảy ra, cũng có vô số cách xử lý, dễ có, khó có, hoặc cố làm cho loằng ngoằng để tỏ ra nguy hiểm :v

## Từ đơn giản nhé, sửa commit trước đó

Để xem, nếu vừa commit 1 đống thứ xong, xog chợt nhận ra message của commit đó bị sai, hoặc k thật sự rõ ràng. Bản thân thấy ngứa mắt và muốn thay đổi nó. Vậy dùng ```commit --amend```

Cụ thể:
```
git commit --amend 
```

Nâng cao hơn chút
```
git commit --amend -m "New commit message"
```

Thêm tuỳ chọn ```-m``` để thay thế cụm "New commit message" vào message của commit hiện tại, đỡ được 1 bước terminal hỏi lại :v

Còn không muốn thay đổi message thì dùng ```--no-edit```

```
git commit --amend --no-edit
```

## Nâng cao hơn tí, giờ thay đổi nội dung commit xem nào

Đã commit xong, nhận ra mình commit thiếu 1 vài file (vd fileA, fileB) thì
```
git add fileA fileB
git commit --amend --no-edit
```
Dễ nhể :v

Commit thừa vài file (vd fileA, fileB) thì
```
git reset HEAD~ fileA fileB
git commit --amend --no-edit
```
Không dám chắc cách trên sẽ thành công trong 1 số trường hợp đặc biệt. Nhưng có 1 cách đảm bảo thành công :v Là xoá commit đó, và commit lại :v
```
git reset HEAD~ 
git add fileA fileB
git commit -m "This is a commit"
```
Ez =)))

## Lên mức nữa nào, giờ muốn thay đổi commit ở sâu hơn thì sao?

Code chán chê, commit chán chê mới nhận ra commit ở tít dưới có vấn đề, ví dụ:

![Screen Shot 2019-03-19 at 4 00 32 PM](https://user-images.githubusercontent.com/14963920/54593031-2cc5ff00-4a60-11e9-8b80-678bd350da27.png)

Hỏng hỏng, phải sửa commit 229ea01 ngay không PM chởi, ae chê cười mất.

Dùng ```git rebase -i``` cứu nguy. ```Rewriting history without a time machine :))```
```
git rebase -i 31acea4
``` 
Sẽ ra cái này

![Screen Shot 2019-03-19 at 4 03 59 PM](https://user-images.githubusercontent.com/14963920/54593220-a2ca6600-4a60-11e9-871c-bdf446502ff7.png)

Mấy cái commands kia thì thôi, giải thích ra không sát nghĩa, mà mất đi tính mạnh mẽ của nó :)) Chủ yếu tôi hay sử dụng pick, reword, edit, drop. Những commands còn lại chưa có dịp thử, chỉ biết rằng nó mạnh lắm :v

Mình cần sửa message của commit 229ea01 nên sẽ sửa thế này
```
reword 229ea01 123
pick 4480a17 update Muốn sửa commit thì làm gì ?.md
pick 89324e9 Do SomeThing
pick 36503e1 Do OtherThing
```
Save lại, sẽ bắn tiếp sang phần sửa message. Sửa message, save lại và hưởng thụ :v

Còn nếu sử dụng edit
```
edit 229ea01 123
pick 4480a17 update Muốn sửa commit thì làm gì ?.md
pick 89324e9 Do SomeThing
pick 36503e1 Do OtherThing
```
Sau khi save lại, sẽ được đưa đến trạng thái sau commit 229ea01. Quay trở lại 2 bài toán thay đổi commit ở bên trên, lại dễ r :v Nhớ rằng sau khi kết thúc quá trình sửa commit, thực hiện ```git rebase --continue``` để tiếp tục quá trình rebase nhé.

Sau khi thấy dòng ```Successfully rebased and updated refs/heads/master.``` mới là kết thúc, dùng ```git log``` check lại thôi.


## Nên hay không nên sửa commit ?

Nên cân nhắc thật kĩ trước khi sửa commit. Nếu commit cần sửa ở xa so với HEAD thì tốt nhất là bỏ qua, làm commit mới. Vì sau khi quyết định edit commit nào, sẽ bắt đầu quá trình rebase các commit kế tiếp, tin tôi đi, rebase tầm 5 commit đã là nhiều rồi chưa kể fix conflict, quá 5 cái là k muốn nữa đâu :scream: Trường hợp khác

```
    B1-B2-B3-B4                       (branchB)
    /
A1-A2-A3-A4-A5-A6                     (branchA)
       \
       C1-C2-C3-C4                    (branchC)
           \
           D1-D2-D3                   (branchD)

```
nếu đứng trên branchA, sửa commit A4 thì chả có gì để nói, nhưng nếu sửa commit A3 thì sao ?

```
    B1-B2-B3-B4                       (branchB)
    /
A1-A2-A3'-A4'-A5'-A6'                 (branchA)
    \
     A3-C1-C2-C3-C4                   (branchC)
            \
            D1-D2-D3                  (branchD)
```
Đứng trên branchA, sửa commit A3 thành A3', tiếp tục rebase các commit tiếp theo, sẽ được A4',A5',A6'. branchC không có gì thay đổi trong lịch sử commit, nhưng bây giờ commit cha chung nhất của branchC và branchA là commit A2, vậy điểm base của branchC được chuyển về A2.

Tương tự, nếu đứng ở branchC, sửa commit A3. Điểm base của branchC đẩy về A2, để ý chút thì thấy branchD không thay đổi đâu :grin:

```
      B1-B2-B3-B4                      (branchB)
      /
     /  C1-C2-D1-D2-D3                 (branchD)
    /  /
A1-A2-A3-A4-A5-A6                      (branchA)
    \
     A3'-C1'-C2'-C3'-C4'               (branchC)
```

Trở lại với vụ đứng ở branchA, sửa commit A3. Giờ nếu muốn trở lại được cây như ban đầu thì sao? Thử ```git cherry-pick``` nhỉ :))

- checkout branchC_new từ commit A3', cherry-pick 4 commit của branchC

```
git checkout branchA
git log --oneline
git checkout <sha-1 of commit A3>
git checkout -b branchC_new
git cherry-pick <sha-1 of commit C1> 
... <fix conflict> ...
git add .
git commit --amend --no-edit

...

git cherry-pick <sha-1 of commit C4> 
... <fix conflict> ...
git add .
git commit --amend --no-edit
``` 

- tương tự, checkout branchD_new từ commit C2', cherry-pick 3 commit của branchD

Kết quả đê

```
    B1-B2-B3-B4                       (branchB)
    /
A1-A2-A3'-A4'-A5'-A6'                 (branchA)
    \  \
     \  C1'-C2'-C3'-C4'               (branchC_new)
      \      \
       \     D1'-D2'-D3'              (branchD_new)
        \
         A3-C1-C2-C3-C4               (branchC)
                \
                D1-D2-D3              (branchD)
```
Trông cũng khá giống nhể, xoá branch thừa và đổi tên 2 branch mới thôi

Như bạn thấy đấy, quá trình sửa qua khá nhiều bước, tốn kha khá thời gian. Với những cây lịch sử loằng ngoằng có khi còn tốn hơn nhiều lần thời gian tạo ra 1 commit sửa. Nên hãy suy nghĩ thật kĩ, chọn cách nào là tuỳ ở bạn thôi =)) Muốn cây lịch sử đẹp, chuẩn chỉ từng commit, hay chọn việc nhẹ nhàng, thêm những commit "vá" chỗ cũ :))

## Một số tip khi thay đổi commit
- Nên cẩn thận, sử dụng hợp lý, tránh lạm dụng quá :v

- Luôn sử dụng ```git status``` và ```git log``` (```git log --oneline```) để kiểm tra.

- Mã hash của commit sẽ bị thay đổi sau mỗi lần dùng ```commit --amend```, nên lịch sử của branch ở local và ở remote khác nhau, muốn push lên, phải dùng ```git push -f``` (cẩn thận đấy :v).

- Các commit đã có trên branch sử dụng chung, thì tốt nhất là không nên sửa đổi, vì còn ảnh hưởng đến người khác :)) Trường hợp thật sự cần thiết mới được sửa, và sau khi push lại, hãy nhắc những người code chung, để phòng trừ hậu hoạ về sau.

Bạn:
```
git push -f origin master
```
Bạn của bạn :v 
```
git fetch
git checkout master
git reset origin/master --hard
```
- Nếu có sai sót khi đang sửa commit, vẫn có thể quay đầu lại vì vẫn còn trên remote. Chưa quen thì có thể backup sang 1 branch ở local. Còn quen rồi thì vô tư đi :v

- Đang ```rebase -i``` mà có vấn đề thì ```git rebase --abort``` - như chưa hề có cuộc chia ly :))).

## Tạm kết
Đó là 1 vài trick nhỏ tôi vẫn hay dùng, sẽ còn update thêm nếu có trick mới :v
