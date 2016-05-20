##Identity, Authentication, and access management in OpenStack.

- Identity : Là sự nhận dạng của những người đang cố truy cập vào tài nguyên điện toán đám mây. Trong OpenStack Keystone, Identity thường được biểu diễn như là một người sử dụng. Trong triển khai đơn giản, Identity của một người sử dụng được lưu trữ trong cơ sở dữ liệu riêng của Keystone. Trong sản suất hoặc môi trường doanh nghiệp external Identity được được sử dụng phổ biến. 
- Authentication : Là quá trình xác minh danh tính người dùng, trong nhiều trường hợp chứng thực ban đầu được thực hiện bởi một đăng nhập với user identity của họ. Trong môi trường OpenStack thô sơ Keystone có khả năng biểu diễn tất cả các bước chứng thực, điều này không được khuyến cáo trong môi trường sản xuất và môi trường doanh nghiệp. ĐỐi với môi trường thực sự nơi mật khẩu cần được bảo vệ và quản lý, Keystone là pluggable như vậy mà nó dễ dàng tích hợp với một hệ thống với một dịch vụ xác thực hiện có như là LDAP hay Active Directory.
- Trong khi một User Identity thường ban đầu xác định bằng mật khẩu , nó rất phổ biến như là một phần của xác thực ban đầu này để tạo ra một mã thông báo xác thực cho sau này . Điều này làm giảm số lượng của tầm nhìn tiếp xúc của các mật khẩu mà cần được giấu kín và bảo vệ nhiều càng tốt. OpenStack dựa chủ yếu trên thẻ để xác thực và xác mục đích khác. Keystone là một dịch vụ OpenStack duy nhất có thể cung cấp cho họ, hiện nay Keystone sử dụng một hình thức mã thông báo được gọi là "a bearer token" điều này có nghĩa là bất kì ai đã có quyền sở hữu dấu hiệu cho dù là đúng hay sai (cho dù đã bị đánh cắp) có khả năng sử dụng thẻ để xác thực và truy cập tài nguyên. Kết quả là, sau khi sử dụng Keystone nó rất quan trọng để bảo vệ thẻ và tài nguyên của họ. 
- Access Management (Authorization) : Một khi user identity được xác thực và một token đã được tạo ra và được phân bố thì mọi thứ bắt đầu trở lên thật thú vị. Điều này là bởi vì bây giờ chúng ta có đủ nền tảng để bắt đầu thực hiện quản lý truy cập. Access management cũng được gọi là Authorization, là quá trình xác định các nguồn lực mà người dùng được phép truy cập. Môi trường Cloud như OpenStack cung cấp cho người dùng với quyền truy cập đến lượng lớn các nguồn tài nguyên. Ví dụ : Cần phải có một cơ chế để xác định mà người dùng được phép tạo ra instances mới của một máy ảo cụ thể, người dùng được phép đính kèm hoặc xóa một volume của blog storage, trong đó người dùng cho phép tạo ra các mạng ảo, vvv,.... Trong OpenStack, Keystone ánh xạ lên  User từ các project hoặc các domain bằng cách kết hợp một vai trò cho người dùng bằng cách 

####In OpenStack, Keystone maps Users to
Projects or Domains by associating a Role for the User for that Project or Domain.

Các Project OpenStack khác như nova, cinder và neutron kiểm tra tài khoản của Project và vai trò bằng cách đánh giá thông tin này sử dụng policy. Các công cụ Policy kiểm tra thông tin này (đặc biệt là giá trị Role) làm cho một quyết định về những hành động của người sử dụng được phép thực hiện.

####Keystone’s Primary Benefits

- Trong khi Keystone chủ yếu tập chung vào cung cấp Identity, Authentication, and Access. Quản lý nó cung cấp một lượng lớn các lợi ích cho một môi trường OpenStack. Những lợi ích chính bao gồm : 
 <ul>
 <li>Xác thực đơn lẻ và quản lý truy cập giao diện cho OpenStack service khác. Keystone xử lý các nhiệm vụ phức tạp của việc tích hợp bên ngoài hệ thống xác thực và cung cấp việc quản lý thống nhất cho tất cả các truy cập vào các dịch vụ OpenStack khác, chẳng han như nova, glane, cinder,.... và do đó Keystone cô lập tất cả các dịch vụ khác từ việc biết làm thế nào để giao tiếp với nhau Identity và authorization cung cấp.</li>
 <li>Keystone cung cấp một đăng ký của container (project) mà OpenStack service khác có thể sử dụng để phân biệt các nguồn lực (ví dụ: servers, image,....)</li>
 <li>Keystone cung cấp một đăng ký domain được sử dụng để xác định không gian tên riêng cho User, Group, và các project để cho phép sự phân biệt giữa các khách hàng.</li>
 <li>Một registry của Role sẽ được sử dụng cho phép giữa Keystone và tập tin Policy của mỗi dịch vụ OpenStack</li>
 <li>Một cửa hàng chuyển nhượng cho phép người dùng và nhóm được giao vai trò cho các project và các domain.</li>
 <li>Một mục lưu trữ các dịch vụ của OpenStack, enpoint, và các khu vực cho phép khách hàng khám phá các dịch vụ hoặc các enpoint mà họ cần.</li>
 </ul>

##Căn bản về Keystone.

###1. Các khái niệm trong Keystone.
####1.1. Một project là gì?

- Trong Keystone một project là một khái niệm trìu tượng được sử dụng bởi các dịch vụ OpenStack khác vào nhóm và cô lập các nguồn tài nguyên (ví dụ : server, image,...)
- Mục đích chính của Keystone là để đăng ký Project và trình bày rõ ai sẽ có quyền truy cập vào các Project. Các Project của họ không phải là của riêng Users nhưng Users hoặc Group của người dùng được phép truy cập vào một Project và sử dụng các khái niệm về Role.

####1.2. Một domain là gì?

- Trong những ngày đầu của OpenStack không có cơ chế để hạn chế tầm nhìn của các Project cho các tổ chức sử dụng khác nhau. Điều này có thể dẫn đến va chạm vô ý
vào tên dự án của các tổ chức khác nhau. Tên người dùng cũng có tầm nhìn toàn cầu và cũng có thể dẫn đến va chạm không mong muốn về tên người dùng nếu hai tổ chức khác nhau mà cả hai cùng sử dụng tên người dùng.
- Để cho OpenStack clouds hỗ trợ tốt hơn nhiều tổ chức sử dụng cùng một lúc, Keystone thêm một khái niệm trừu tượng mới, được gọi là một miền, có thể cung cấp khả năng để cô lập tầm nhìn của một tập hợp các Project và người sử dụng (và các nhóm thành viên) cho một tổ chức cụ thể.
-  Một miền được định nghĩa chính thức là một bộ sưu tập của người dùng, nhóm, và các dự án. Tên miền cho phép bạn phân chia các nguồn lực trong đám mây của bạn vào không gian lưu trữ được sử dụng bởi các tổ chức cụ thể. Một miền có thể phục vụ như là một bộ phận hợp lý giữa các phần khác nhau của một doanh nghiệp, hoặc mỗi miền có thể đại diện cho các doanh nghiệp hoàn toàn riêng biệt.

####1.3. Users và User Group (Actors).

- Trong lĩnh vực Keystone, Users and User Groups là các thực thể được tiếp cận với các nguồn tài nguyên
được phân lập ở miền và các dự án.
- Groups là một bộ sưu tập của người dùng. Người dùng
là cá nhân sẽ kết thúc bằng cách sử dụng điện toán đám mây của bạn.

**Đồ thị biểu diễn.**

- Mối quan hệ giữa các domain, project, user, và các group được hiển thị dưới đây.

![scr1](http://i.imgur.com/2Owpw8p.png)

-  User,group, và các Project sẽ luôn là "domain scoped," có nghĩa là một tên cho một người sử dụng,
nhóm, và dự án có thể được phổ biến trên các lĩnh vực.

**Roles**
- Roles được sử dụng trong Keystone để truyền đạt một ý nghĩa của sự ủy quyền. Một diễn viên có thể có
rất nhiều vai vào một mục tiêu. Làm thế nào các vai trò được sử dụng để xác định quyền được bảo hiểm.
Trong "1.4 Quản lý truy cập và ủy quyền" Ví dụ,
Vai trò của quản trị là "giao cho" người sử dụng "bob" và nó được "phân công vào" dự án "phát triển."

**Assignment**

- Một role assignment là một ternary (hoặc ba): sự kết hợp của một actor, một mục tiêu, và một vai trò. nhiệm vai trò được cấp và thu hồi, và có thể được di truyền giữa nhóm và người sử dụng, domain và project.




**Targets**

- Project và Domain là rất giống nhau ở chỗ cả hai đều là các nơi mà vai trò là "Gán vào." Nói cách khác, một người dùng hoặc Nhóm người dùng được phép truy cập đến hoặc là một dự án hoặc tên miền bằng cách gán một giá trị Vai trò đặc biệt cho User hoặc User Group cho một cụ thể Dự án hoặc tên miền. Bởi vì dự án và tên miền có đặc điểm tương tự như vậy, khi chúng ta cần phải đề cập đến cả hai thực thể chung, chúng tôi giới thiệu cho họ như các mục tiêu.

**Một token là gì?**

- Để cho một người dùng để gọi bất kỳ API OpenStack họ cần phải:
 <ul>
 <li>chứng minh họ là ai</li>
 <li>và họ nên được phép gọi các API trong câu hỏi.</li>
 </ul>

- Cách họ đạt được đó là bằng cách đi qua một thẻ OpenStack vào các API gọi và Keystone là Open-Stack service có trách nhiệm tạo ra các thẻ.
- Một người sử dụng nhận được thẻ này khi
xác thực thành công trở lại Keystone. Các mã thông báo cũng mang theo nó cho phép. Nó chứa các phép một người sử dụng có trên các cloud. Một thẻ có cả ID và tải trọng. ID của một mã thông báo được đảm bảo là duy nhất cho mỗi cloud, và tải trọng chứa dữ liệu về người dùng.

**Một catalog là gì?**

- Danh mục dịch vụ là điều cần thiết cho một đám mây OpenStack. Nó chứa các URL và các thiết bị đầu cuối
của các dịch vụ đám mây khác nhau. Nếu không có các cửa hàng, người dùng và các ứng dụng sẽ không biết nơi để định tuyến các yêu cầu để tạo máy ảo hoặc lưu trữ các đối tượng. Danh mục dịch vụ này được chia thành một danh sách các thiết bị đầu cuối, và mỗi thiết bị đầu cuối được chia thành URL admin, URL nội bộ, và URL công cộng.

###2. Identity.

- Dịch vụ nhận dạng của Keystone cung cấp các Actors. Bản sắc trong các đám mây có thể đến từ các địa điểm khác nhau, bao gồm nhưng không giới hạn SQL, LDAP, và Federated.

####2.1. SQL.

- Keystone bao gồm các tùy chọn để lưu trữ của Actors (Users and Groups) trong SQL; hỗ trợ cơ sở dữ liệu bao gồm MySQL, PostgreSQL, và DB2.
-  Keystone sẽ lưu trữ thông tin chẳng hạn như tên, mật khẩu, và mô tả. Các thiết lập cho các cơ sở dữ liệu phải được quy định trong tập tin cấu hình của Keystone. Về cơ bản, Keystone hoạt động như một Identity provider.  trong đó có thể không phải là trường hợp tốt nhất cho tất cả mọi người, và chắc chắn không phải là trường hợp tốt nhất cho khách hàng doanh nghiệp. Sau đây là một bản tóm tắt ngắn gọn về những lợi thế và nhược điểm của việc sử dụng tùy chọn SQL Identity :
-   
**Ưu điểm**

 <ul>
 <li>Dễ dàng cài đặt</li>
 <li>Quản lý người dùng và nhóm thông qua OpenStack API</li>
 </ul>

**Nhược điểm** 
 
 <ul>
 <li>Keystone không phải là một Identity Provider</li>
 <li>Hỗ trợ mật khẩu khá yếu
  <ul>
  <li>Không luân chuyển mật khẩu</li>
  <li>Không khôi phục mật khẩu</li>
  </ul>
 </li>
 <li>Hầu hết các doanh nghiệp có một máy chủ LDAP họ muốn sử dụng</li>
 <li> Nhận Dạng silo: chưa một tên người dùng và mật khẩu người dùng phải nhớ</li>
 </ul>

####2.2. LDAP.

- Keystone cũng có tùy chọn để lấy và lưu trữ của Actors (Users and Groups) trong Lightweight Directory Access Protocol (LDAP).
- Keystone sẽ truy cập vào LDAP chỉ giống như bất kỳ ứng dụng nào khác sử dụng LDAP (Hệ thống đăng nhập, Email, ứng dụng web,vv). Các thiết lập cho kết nối với LDAP được quy định trong cấu hình của Keystone file. Các tùy chọn này cũng bao gồm liệu Keystone có thể ghi vào LDAP hoặc đơn giản đọc dữ liệu LDAP.
-  Lý tưởng nhất, LDAP chỉ nên biểu diễn các hoạt động đọc, chẳng hạn như người sử dụng và tra cứu nhóm (thông qua tìm kiếm) và xác thực (thông qua ràng buộc). Nếu sử dụng LDAP như một công cụ chỉ đọc nhận dạng phụ trợ, Keystone nên cần một số tiền tối thiểu của các đặc quyền để sử dụng LDAP. Ví dụ, nó cần truy cập đọc đến người dùng và nhóm các thuộc tính quy định tại keystone.conf, một tài khoản có đặc quyền (truy cập nặc danh được ưa thích), và nó không yêu cầu quyền truy cập vào các hash mật khẩu. Những lợi thế và bất lợi các tùy chọn LDAP nhận dạng được hiển thị dưới đây.

![scr2](http://i.imgur.com/h5cPTXL.png)

**Ưu điểm**
 <ul>
 <li>Không còn cần phải duy trì các bản sao của tài khoản người dùng.</li>
 <li>Keystone không hành động như một Identity Provider (ko hành xác thực tập chung).</li>
 </ul>

**Nhược điểm**
 <ul>
 <li>Service account vẫn cần phải được lưu trữ ở đâu, khi các quản trị viên có thể LDAP không muốn các tài khoản trong LDAP.</li>
 <li>Keystone vẫn còn "nhìn thấy" mật khẩu người dùng, kể cả khi các mật khẩu trong việc chứng thực yêu cầu. Keystone chỉ đơn giản là chuyển những yêu cầu này, nhưng ý tưởng Keystone không muốn nhìn thấy mật khẩu của người dùng, bao giờ hết!</li>
 </ul>

####2.3. Multiple Backends.

- Như của việc phát hành Juno, Keystone hỗ trợ nhiều phần phụ trợ nhận dạng cho V3 Identity API.
- Tác động của việc này là một triển khai có thể có một nguồn Identity (backend) trên mỗi miền Keystone. Các tên miền mặc định thường là một phụ trợ SQL, vì nó là sử dụng để lưu trữ các tài khoản dịch vụ.
-  LDAP thường bị hạn chế chỉ được sử dụng cho nhân viên thông tin. Một lợi ích của một phân chia hợp lý giữa các phần Identity backend và domain là bây giờ nhiều LDAP đã có thể được sử dụng. Vì vậy, trong trường hợp của một công ty sáp nhập hoặc khác phòng ban đã có LDAP khác nhau, cùng một doanh nghiệp có thể vẫn còn được đại diện. Những lợi thế và bất lợi của nhiều phần phụ trợ như sau.

![scr3](http://i.imgur.com/FaFOKNy.png)

**Ưu điểm**
 <ul>
 <li>Có khả năng hỗ trợ đồng thời nhiều LDAP đã cho tài khoản người dùng khác nhau và phần phụ trợ SQL cho các tài khoản dịch vụ.</li>
 <li>Tận dụng hiện Identity LDAP trong khi không ảnh hưởng đến nó</li>
 </ul>
**Nhược điểm**

 <ul>
 <li>Hơi phức tạp hơn để thiết lập.</li>
 <li>Xác thực tài khoản người dùng phải được domain scoped</li>
 </ul>

####2.4. Identity Providers.

- Như của việc phát hành Icehouse, Keystone là khả năng xử lý xác thực thông qua Apache module cho nhiều nhà cung cấp nhận dạng đáng tin cậy. Những người dùng này không được lưu trữ trong Keystone, và đang được coi là phù du. Người sử dụng liên sẽ có các thuộc tính của họ ánh xạ vào nhiệm vai trò dựa trên nhóm.
- Từ một quan điểm Keystone, một Identity provider là một nguồn cho Identity; nó có thể tham khảo phần mềm được hỗ trợ bởi nhiều phần phụ trợ (LDAP, AD, MongoDB) hoặc Social Logins (Google, Facebook, Twitter).
- Về cơ bản, nó là phần mềm  (IBM’s Tivoli Federated Identity Manager, for instance) mà trừu tượng hóa ra backend và dịch sử dụng thuộc tính vào một Identity liên chuẩn định dạng giao thức (SAML, OpenID Connect). 
Điều này cũng phù hợp hơn trong Keystone's greater
có kế hoạch để giảm tải việc xác thực và nhận dạng các phần liên quan đến một dịch vụ đã thực hiện điều này trong một doanh nghiệp. Cách tiếp cận nhà cung cấp danh có nhiều lợi thế và vài nhược điểm.

**Lợi thế**

 <ul>
 <li>Có khả năng tận dụng cơ sở hạ tầng và phần mềm hiện có để xác thực người dùng và
lấy thông tin về người sử dụng.</li>
 <li>tách nhiều hơn nữa giữa Keystone và thông tin nhận dạng xử lý</li>
 <li>Mở cửa cho khả năng mới trong các lĩnh vực liên bang, chẳng hạn như signon đơn và đám mây lai</li>
 </li>Keystone không thấy bất kỳ mật khẩu người dùng</li>
 <li>Cung cấp nhận dạng xử lý xác thực hoàn toàn, vì vậy cho dù đó là mật khẩu, giấy chứng nhận, hoặc hai yếu tố dựa trên là không liên quan đến Keystone.</li>
 </ul>

**Nhược điểm**

 <ul>
 <li>Hầu hết các thiết lập phức tạp của Identity sources.</li>
 </ul>

####2.5. Các trường hợp sử dụng Identity backends.


**SQL :**
 <ul>
 <li>Sử dụng SQL nếu bạn đang thử nghiệm hoặc phát triển với OpenStack.</li>
 <li>Cài đặt nhỏ của người sử dụng.</li>
 <li>OpenStack cụ thể tài khoản (service users.)</li>
 </ul>

**LDAP :**
 <ul>
 <li>Sử dụng LDAP nếu nó được đặt ra trong doanh nghiệp của bạn.</li>
 <li>Chỉ sử dụng LDAP nếu bạn có thể tạo ra dịch vụ tài khoản cần thiết trong LDAP.</li>
 </ul> 

**Mutiple backends :**
 <ul>
 <li>Phương pháp ưa thích cho hầu hết các doanh nghiệp.</li>
 <li>Sử dụng nếu người dử dụng dịch vụ không được phép vào LDAP.</li>
 </ul>

**Identity Provider** 

 <ul>
 <li>Khi bạn muốn sử dụng lợi thế của cơ chế new Federated Identity.</li>
 <li>Sử dụng nếu một nhà cung cấp Identity đã tồn tại (đối với đăng nhập một lần).</li>
 <li>Keystone không thể truy cập LDAP.</li>
 <li>Không có LDAP Identity source.</li>
 <li>Sử dụng nếu tương tác LDAP được chuyển tới nền tảng cơ bản và máy chủ web.</li>
 </ul> 

###3. Authentication.

- Có rất nhiều cách để xác thực với dịch vụ ở Keystone. Tuy nhiên có hai các phổ biến nhất là cung cấp một mật khẩu hoặc cung cấp một mã thông báo (token.)

####3.1. Password.

- Cách phổ biến nhất đối với người sử dụng dịch vụ để xác thực là cung cấp một mật khẩu.
- Các payload dưới đấy là các mẫu POST yêu cầu đến Keystone. ĐÓ là lợi ích để hiển thị toàn bộ payload để người đọc có thể nhận ra thông tin đó là để xác thực.

**Về payload và chú ý về domains**

- Payload là một phần của dữ liệu được truyền, đó là thống điệp đích thực. Payload không bao gồm các thông tin gửi đi với nó như tiêu đề hay metadata, đôi khi được gọi là dữ liệu trên không, gửi chỉ đề đủ điều kiện giao hàng Payload.
- Payload của request phải đầy đủ thông tin để tìm nơi người dùng tồn tại, xác thực người dùng và tùy chọn truy xuất danh mục dịch vụ dựa trên quyền sử dụng của người sử dụng trên một phạm vi (Project).
- Phần người sử dụng xác định người dùng đến nên có thông tin tên miền (hoặc tên miền hoặc ID), trừ khi toàn cầu chỉ có duy nhất ID của người dùng được sử dụng trong đó, trường hợp đó là đủ để xác định người sử dụng. Điều này là vì trong một triển khai đa miền có thể có nhiều người sử dụng cùng tên, nên phạm vi thích hợp là cần thiết để xác định người dùng được chứng thực.
- Phần phạm vi là tùy chọn nhưng thường được sử dụng, vì ko có một phạm vi người sử dụng không thể truy xuất danh mục dịch vụ. Phạm vi được sử dụng để chỉ ra các dự án người sử dụng muốn làm việc lại. Nếu một người dùng không có vai trò trong dự án, yêu cầu sẽ bị từ chối.
- Tương tự như phần người dùng phần phạm vi phải có đủ thông tin về dự án để tìm thấy nó, vì vậy các miền sở hữu phải được xác định. Như trong trường hợp của người dùng và các nhóm, tên dự án cũng có thể xung đột trên domain. Tuy nhiên phải được đảm bảo là duy nhất nếu được chỉ định, không thông tin tên miền là cần thiết.

![scr4](http://i.imgur.com/prmEzoJ.png)

####3.2. Token.

- Người dùng có thể yêu cầu một token mới bằng cách cung cấp một mã thông báo hiện tại 
- Tải trọng yêu cầu của POST này là ít hơn so với phải dùng Password.
- Có nhiều lý do tại sao một token sẽ được sử dụng để khô phục, giống như làm mới một mã thông báo rằng sẽ sớm hết hạn hoặc thay đổi một token từ unscoped token đến scoped token.

![scr5](http://i.imgur.com/GhJ2Dw8.png)

###4. Access Management and Authorization.

- Keystone là quan trọng đối với OpenStack. Các tiếp cận của Keystone cho vấn đề này là tạo ra một Role-Based Access Control (RBAC) policy được thi hành trên mỗi API enpoint.
- Những chính sách này được lưu trữ trong một tập tin trên đĩa và thường được đặt tên là policy.json.
- Mỗi quy tắc bắt đầu với Identity và chỉ rõ một controller bảo vệ và quản lý một API. Chúng ta sử dụng các mục tiêu đã được thành lập để bảo vệ những mục tiêu mới. Bảng dưới đây biểu thị các mục tiêu mà bảo vệ API : 

![scr6](http://i.imgur.com/1O6xPb4.png)

- Điều quan trọng là cần lưu ý sự khác biệt giữa liệt kê giữa tất cả các dự án và danh sách một dự án của một người dùng. Bảng liệt kê tất cả các dự án một người dùng có quyền truy cập vào nên không chỉ được giới hạn để quản trị viên, mà còn để người sử dụng có vai trò trong dự án đó.

###5. Backends and services.

- NOTE !!!

![scr7](http://i.imgur.com/VFppctT.png)


###6. FAQs

**What’s the difference between a Domain and a Region?**

- Những thuật ngữ thường được sử dụng thay thế cho nhau trong thế giới OpenStack, nhưng các khái niệm rất khác nhau. Một khu vực được dự định là một đại diện địa lý: là doanh nghiệp có thể có một khu vực Mỹ-Tây và khu vực Mỹ-Đông. Một tên miền là một phân chia hợp lý giữa quyền sở hữu của các Project và các Identity Sources.

**Can users exist in multiple domains?**

- Mỗi người dùng được sở hữu bởi một tên miền. Một điểm quan trọng của sự nhầm lẫn là tên người dùng có thể là domains trên cùng. Có nghĩa là có thể có một stevemar tại IBM domain và một stevemar tại domain Acme Inc. Mỗi người sẽ có riêng mình ID vì mỗi stevemar thuộc sở hữu của một tên miền khác nhau, và được coi là của Keystone, là một người sử dụng khác nhau.

**What is the difference between a scoped and unscoped token?**

- Một unscoped token là một trong những nơi mà người dùng được xác thực nhưng không phải là cho một Project cụ thể hoặc domain. Đây là loại token hữu ích cho việc truy vấn như là xác định những gì dự án một người dùng có quyền truy cập vào. Một dấu hiệu chỉnh phạm vi được xảy ra khi người dùng được xác thực cho một dự án hay một domain cụ thể.
- Scoped token có vai trò thông tin liên quan đến Scoped tokens và các loại token được sử dụng bởi OpenStack service để xác định những loại hoạt động được phép.

##Hãy sử dụng Keystone.

- Trong chương này chúng ta sẽ tìm hiểu xem làm thế nào để sử dụng Keystone trong một môi trường phát triển. Điều này liên quan đến một vài bước. 
 <ul>
 <li>Triền khai OpenStack bằng Devstack.</li>
 <li>Sau đó chúng ta thử cơ bản hoặt động của Keystone với OpenStack client.</li>
 <li>Sau đó chúng ta thực hiện các hoạt động cùng với Keystone Horizon.</li>
 </ul>

- Chúng ta sẽ có cung cấp các lựa chọn thay thế cURL đến OpenStack client để minh họa thực tế rằng CLI chỉ là một wrapper cho một REST call.

###1. Getting DevStack.

#### Hướng dẫn cài đặt devstack.
- Trên một node

##### Bước 1:
- Chuẩn bị một máy Ubuntu 14.04 Server 64 bit với cấu hình
```sh
Cấu hình tối thiểu
 - RAM 4GB
 - HDD:60GB
 - NIC: 01: 192.168.1.0/24

Phiên bản hệ điều hành: Ubuntu Server 14.04 64 bit
 
```

- Đăng nhập bằng tài khoản root và thực hiện các lệnh sau để update bản mới nhất 
```sh
apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y && init 6
```

##### Bước 2:
- Đăng nhập vào OS với tài khoản root.
- Tạo và gán cấu hình sudo cho user `stack`
```sh
adduser stack
apt-get -y install sudo 
apt-get -y install git
echo "stack ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

```

- Chuyển từ tài khoản `root` sang tài khoản `stack` :
```sh
su - stack
```

##### Bước 3: Tải mã nguồn của OpenStack trong repos devstack
- Clone git của devstack về bằng lệnh git
- Mặc định (nhánh master) trong git của devstack là source mới nhất của OpenStack.
- Trong git của devstack chứa các phiên bản của OpenStack (Mitaka, Liberty, Kilo, Juno ...)
- Trong hướng dẫn này sẽ thực hiển tải bản OpenStack Liberty
```sh
git clone -b stable/liberty https://github.com/openstack-dev/devstack.git
```

#### Bước 4: Tạo file local.conf
- Các shell trong devstack sẽ tham chiếu tới file local.conf để lấy giá trị các `biến` khi thực thi các dòng lệnh trong shell đó.
- File local cần phải các các khao báo tối thiểu về password cho các dịch vụ trong OpenStack như: MySQL, RabbitMQ .... Các biến còn lại sẽ lấy giá trị mặc định.
- Tùy vào tính năng cần test mà người dùng có thể khai báo trong file local.conf
- Trong ví dụ này chúng tôi sử dụng dải mạng 192.168.1.0/24 cho dải EXTERNAL
- Di chuyển vào thư mục `devstack`
```sh
cd /devstack/
```

-Tạo file local.conf với nội dung mẫu như sau, lưu ý tùy vào tình huống mà có thể sửa file này cho phù hợp.
```sh
[[local|localrc]]
DEST=/opt/stack

# Khai bao log cho devstack
LOGFILE=$DEST/logs/stack.sh.log
VERBOSE=True
SCREEN_LOGDIR=$DEST/logs/screen
OFFLINE=False

# Khai bao IP cua may cai dat devstack
HOST_IP=192.168.1.110

# Khai bao mat khau cho cac dich vu
ADMIN_PASSWORD=vietstacker
MYSQL_PASSWORD=vietstacker
RABBIT_PASSWORD=vietstacker
SERVICE_PASSWORD=vietstacker
SERVICE_TOKEN=vietstacker


disable_service n-net
enable_service q-svc
enable_service q-agt
enable_service q-dhcp
enable_service q-meta
enable_service q-l3

#ml2
Q_PLUGIN=ml2
Q_AGENT=openvswitch

# vxlan
Q_ML2_TENANT_NETWORK_TYPE=vxlan

# Networking
FLOATING_RANGE=192.168.1.0/24
Q_FLOATING_ALLOCATION_POOL=start=192.168.1.150,end=192.168.1.200
PUBLIC_NETWORK_GATEWAY=192.168.1.1

FIXED_RANGE=172.16.0.0/24
NETWORK_GATEWAY=172.16.0.1

PUBLIC_INTERFACE=eth0

Q_USE_PROVIDERNET_FOR_PUBLIC=True
Q_L3_ENABLED=True
Q_USE_SECGROUP=True
OVS_PHYSICAL_BRIDGE=br-ex
PUBLIC_BRIDGE=br-ex
OVS_BRIDGE_MAPPINGS=public:br-ex


# Neu dung de dev thi thay enable_service cho dong duoi
disable_service tempest


#vnc
enable_service n-novnc
enable_service n-cauth
```
- Trong file khai báo trên đã thực hiện
 - Khai báo mật khẩu cho các dịch vụ
 - Kích hoạt Neutron cho devstack (từ bản Kilo trở đi, mặc định devstack sử dụng neutron thay cho nova-network)
 - Dải mạng External là: 192.168.1.0/24
 - Dải mạng Internet là: 172.16.0.0/24
 - Sau khi cài devstack với file local trên xong, devstack sẽ tạo ra dải mạng external và bắt đầu từ 192.168.1.150 tới 192.168.1.200
 - Thời gian cài đặt devstack có thể nhanh hay chậm tùy thuộc vào tốc độ internet. 
 - Có thể sử dụng các kỹ thuật repos, apt-cache để tăng tốc độ download các package khi cài devstack.

##### Bước 5: thực thi script
- Đảm bảo lúc này bạn đang ở thư mục /home/stack/devstack/ nhé và thực thi shell sau.
```sh
./stack.sh
```

##### Các chú ý khi khởi động lại devstack
- Fix lỗi Cinder
```sh
- Gõ lệnh dưới trước khi chạy .rejoin-stack

sudo losetup /dev/loop0 /opt/stack/data/stack-volumes-default-backing-file
sudo losetup /dev/loop1 /opt/stack/data/stack-volumes-lvmdriver-1-backing-file

```
- Sau khi khơi động lại devstack cần thực hiện lệnh sau: `./home/stack/rejoin-stack.sh`


Tham khảo : https://github.com/vietstacker/devstack-note

###2. Keystone hoạt động cơ bản sử dụng OpenStack client.

- Trong phần này chúng ta sẽ sử dụng OpenStackClient để thực hiện các hoạt động cơ bản Keystone chẳng hạn như chứng thực, niêm yết, và tạo ra người sử dụng, các dự án, lĩnh vực, các nhóm, và vai trò.
- Đối với mục đích minh họa, chúng tôi cũng bao gồm việc thay thế cURL đến cụ thể lệnh để cho người đọc thấy OpenStackClient thực hiện tương đương REST call.
- Trước khi chúng ta bắt đầu thực hiện lệnh, chúng ta nên tạo một số biến môi trường để chứng thực với dịch vụ của Keystone dễ dàng hơn. Lưu ý rằng hầu hết các giá trị nên giống nhau cho việc triển khai của bạn, nhưng tên máy chủ hoặc địa chỉ IP của bạn VM có thể khác nhau.

```sh
$ export OS_IDENTITY_API_VERSION=3
$ export OS_AUTH_URL=http://10.0.2.15:5000/v3
$ export OS_USERNAME=admin
$ export OS_PROJECT_NAME=admin
$ export OS_USER_DOMAIN_NAME=Default
$ export OS_PASSWORD=openstack
$ export OS_PROJECT_DOMAIN_NAME=Default
```

Để kiểm tra xem biến môi trường tiết lập hay chưa thực hiện lệnh sau và kiểm tra đầu ra :

```sh
$ env | grep OS
OS_IDENTITY_API_VERSION=3
OS_AUTH_URL=http://10.0.2.15:5000/v3
OS_USERNAME=admin
OS_PROJECT_NAME=admin
OS_USER_DOMAIN_NAME=Default
OS_PASSWORD=openstack
OS_PROJECT_DOMAIN_NAME=Default
```

####2.1. Getting a token.

**Using OpenStack client**

- Sau khi chúng ta thiết lập xác thực và ủy quyền dữ liệu như các biến môi trường, chúng ta chỉ đơn giản thực hiện mã để phát hành một thông báo. 

```sh
$ openstack token issue
```

**Sử dụng cURL**

- Khi sử dụng cURL để nhận token, tải trọng cho các yêu cầu chứng thực phải bao gồm thông tin về người sử dụng và dự án.

####2.2. Listen User.

**Using OpenStack client**

- Sau khi chạy DevStack, một số người dùng sẽ được tạo ra tự động. Nói chung là các dịch vụ tài khoản của OpenStack (Cinder, Glane, và Nova), một tài khoản quản trị (admin), và một tài khoản không phải quản trị (demo).

- Câu lệnh kiểm tra 

```sh
$ openstack user list
```

![scr2](http://i.imgur.com/CU7QkKC.png)

**Using cURL**

- Việc thay thế sẽ được sử dụng một token và đạt API chính xác.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
http://localhost:5000/v3/users | python -mjson.tool
{
"links": {
"next": null,
"previous": null,
"self": "http://localhost:5000/v3/users"
},
"users": [
{
"domain_id": "default",
"enabled": true,
"id": "0aa473010af04acb86018125ef71a65e",
"links": {
"self": "http://localhost:5000/v3/users/0aa473010a...125ef71a65e"
},
"name": "glance"
},
{
"domain_id": "default",
"enabled": true,
"id": "9644330bc62542c491179895c6a6d228",
"links": {
"self": "http://localhost:5000/v3/users/96443...91179895c6a6d228"
},
"name": "admin"
},
{
"domain_id": "default",
"enabled": true,
"id": "ab7ab1f3fe2745fd91b3d62fd3b7309b",
"links": {
"self": "http://localhost:5000/v3/users/ab7a...d91b3d62fd3b7309b"
},
"name": "cinder"
},
{
"domain_id": "default",
"email": "demo@example.com",
"enabled": true,
"id": "c88b8fa1414e44be918e9e129f04147d",
"links": {
"self": "http://localhost:5000/v3/users/c88b8fa...8e9e129f04147d"
},
"name": "demo"
},
{
"domain_id": "default",
"enabled": true,
"id": "cf15631f360c4db18fc95fe95da827f2",
"links": {
"self": "http://localhost:5000/v3/users/cf1563...fc95fe95da827f2"
},
"name": "nova"
}
]
}
```

####2.3. Listing project.

**Using OpenStack client**

- Tương tự như user, DevStack cũng tạo ra một số dự án theo mặc định. Họ có thể được nhìn thấy bởi lệnh sau đây:

```sh
$ openstack project list
```

```sh
$ openstack project list
+----------------------------------+--------------------+
| ID | Name |
+----------------------------------+--------------------+
| 10bc96bf62c14d44b02bb5ad8aef57d3 | admin |
| 241d8b116a164bcb9c63d75117ed3894 | demo |
| 81dc6de893924fbbbf16f272bdfba38d | invisible_to_admin |
| ebad3ef327c143bb8e79a52b9b0324e8 | service |
+----------------------------------+--------------------+
```

**Using cURL**

- Một lần nữa, tương tự như những người sử dụng API, một token được yêu cầu và chúng ta cần phải đạt được đúng enpoint.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
http://localhost:5000/v3/projects | python -mjson.tool
{
"links": {
"next": null,
"previous": null,
"self": "http://localhost:5000/v3/projects"
},
"projects": [
{
"description": "",
"domain_id": "default",
"enabled": true,
"id": "5f198c74b0984566b0a6da26440c5f85",
"is_domain": false,
"links": {
"self": "http://localhost:5000/v3/projects/5f1...0a6da26440c5f85"
},
"name": "demo",
"parent_id": null
},
{
"description": "",
"domain_id": "default",
"enabled": true,
"id": "9457bf6cb940453ab92a88219a40a3f7",
"is_domain": false,
"links": {
"self": "http://localhost:5000/v3/projects/9457bf6cb9...9a40a3f7"
},
"name": "service",
"parent_id": null
},
{
"description": "",
"domain_id": "default",
"enabled": true,
"id": "dcd882c878bd43188f9758831676559a",
"is_domain": false,
"links": {
"self": "http://localhost:5000/v3/projects/9457bf6cb9...9a40a3f7"
},
"name": "service",
"parent_id": null
},
{
"description": "",
"domain_id": "default",
"enabled": true,
"id": "dcd882c878bd43188f9758831676559a",
"is_domain": false,
"links": {
"self": "http://localhost:5000/v3/projects/dcd882c878...1676559a"
},
"name": "invisible_to_admin",
"parent_id": null
},
{
"description": "",
"domain_id": "default",
"enabled": true,
"id": "ed50269971bc41ef9f25ce2c7e9b9d11",
"is_domain": false,
"links": {
"self": "http://localhost:5000/v3/projects/ed5026997...c7e9b9d11"
},
"name": "admin",
"parent_id": null
}
]
}
```


####2.4. Listing group.

**Using OpenStack client**

- Tương tự như người dùng, DevStack cũng tạo ra hai nhóm theo mặc định. Lưu ý rằng người dùng không là một phần của một nhóm nào.

```sh
$ openstack group list
```
![scr3](http://i.imgur.com/s4aFEJg.png)

**Using cURL**

- Như với các lệnh khác, chỉ đơn giản là tái sử dụng các token và đạt enpoint phù hợp.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
http://localhost:5000/v3/groups | python -mjson.tool
{
"groups": [
{
"description": "openstack admin group",
"domain_id": "default",
"id": "5553cf58254746c8ba615b671dcdc5b2",
"links": {
"self": "http://localhost:5000/v3/groups/5553cf582...1dcdc5b2"
},
"name": "admins"
},
{
"description": "non-admin group",
"domain_id": "default",
"id": "8a2e550b2edc4f76bae608e578becf5c",
"links": {
"self": "http://localhost:5000/v3/groups/8a2e550b2...8e578becf5c"
},
"name": "nonadmins"
}
],
"links": {
"next": null,
"previous": null,
"self": "http://localhost:5000/v3/groups"
}
}
```

####2.5. Listing Roles.

**Using OpenStack client**

- Trong một phong cách tương tự như những lệnh trước, DevStack cũng tạo ra một số Role bằng mặc định và gán chúng đến những người dùng khác nhau qua Project.

```sh
$ openstack role list
```

![scr4](http://i.imgur.com/3cGl3B9.png)

**Using cURL**

- Trở lại và sử dụng một thành phần truy cập enpoint có thực.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
http://localhost:5000/v3/roles | python -mjson.tool
{
"links": {
"next": null,
"previous": null,
"self": "http://localhost:5000/v3/roles"
},
"roles": [
{
"id": "20cd9e77e2d44d3490d8c044f508c8f3",
"links": {
"self": "http://localhost:5000/v3/roles/20cd9e77e...044f508c8f3"
},
"name": "service"
},
{
"id": "2d357899bd5c430988772eb861fb7a68",
"links": {
"self": "http://localhost:5000/v3/roles/2d357899b...eb861fb7a68"
},
"name": "admin"
},
{
"id": "40df936c87824fd98b7e39da44f2dbde",
"links": {
"self": "http://localhost:5000/v3/roles/40df936c8...9da44f2dbde"
},
"name": "Member"
},
{
"id": "d1e7fed5471941b9aeebf15a341edef9",
"links": {
"self": "http://localhost:5000/v3/roles/d1e7fed54...15a341edef9"
},
"name": "ResellerAdmin"
},
{
"id": "face2e488b1c4562bc9f43f8a5e143db",
"links": {
"self": "http://localhost:5000/v3/roles/face2e488...3f8a5e143db"
},
"name": "anotherrole"
}
]
}
```

####2.6. Listing Domain.

**Using OpenStack Client**

- Keystone sẽ tự động có một miền duy nhất khi được đưa lên; điều này được xử lý trước tính tương thích giữa các bản API Identity.

```sh
$ openstack domain list
```

![scr5](http://i.imgur.com/s0i3JOC.png)

**Using cURL**

- Liệt kê các domain, sử dụng một token hợp lệ để truy cập Enpoint đúng.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
http://localhost:5000/v3/domains | python -mjson.tool
{
"domains": [
{
"description": "Owns users and projects for Identity API v2.",
"enabled": true,
"id": "default",
"links": {
"self": "http://localhost:5000/v3/domains/default"
},
"name": "Default"
}
],
"links": {
"next": null,
"previous": null,
"self": "http://localhost:5000/v3/domains"
}
}
```

####2.7. Creating another domain.

**Using OpenStack client**

- Trở lên tốt hơn và thiết thực hơn với v3 Identity API, chúng ta hãy tạo một Domain , và một project và một user trở thành sở hữu của domain đó.

```sh
$ openstack domain create acme
```

![scr6](http://i.imgur.com/qTKtSJv.png)

**Using cURL**

- Ở đây chúng ta sử dụng một yêu cầu POST với lượng thông tin tối thiểu trong trọng tải chỉ có domain name theo mặc định của nó mới được kích hoạt.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
-H "Content-Type: application/json" -d '{ "domain": { "name": "acme"}}'
http://localhost:5000/v3/domains | python -mjson.tool
{
"domain": {
"enabled": true,
"id": "05a0fcbf796142bfbeb30d0fd3dfa67a",
"links": {
"self": "http://localhost:5000/v3/domains/05a0fcbf796142b...d3dfa67a"
},
"name": "acme"
}
}
```

####2.8. Creat a project within the Domain.

**Using OpenStack client**

 - Để tạo ra một dự án trong Domain , chúng ta cần chỉ định domain chúng ta đã tạo trong bước trước và tốt nhất là cung cấp cho nó một mô tả.

```sh
$ openstack project create tims_project \
--domain acme \
--description "tims dev project"
```

![scr7](http://i.imgur.com/9Layp99.png)

**Using cURL**

- Cùng với tên của project các tải trọng yêu cầu POST cũng phải xác đinh ID domain, ID domain có thể tìm thấy trong các bước trước . Các mô tả của Project là tùy chọn.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
-H "Content-Type: application/json" \
-d '{ "project": { "name": "tims_project", \
"domain_id": "05a0fcbf796142bfbeb30d0fd3dfa67a", \
"description": "tims dev project"}}' \
http://localhost:5000/v3/projects | python -mjson.tool
{
"project": {
"description": "tims dev project",
"domain_id": "05a0fcbf796142bfbeb30d0fd3dfa67a",
"enabled": true,
"id": "26436cea94ac4a40b890f05434f15aa4",
"is_domain": false,
"links": {
"self": "http://localhost:5000/v3/projects/26436cea94ac4...434f15aa4"
},
"name": "tims_project",
"parent_id": null
}
}
```

####2.9 . Creat a User within the Domain.

**Using OpenStack client.**

- Để tạo một người dùng trong Domain , chúng ta cần phải chỉ định tên miền chúng ta đã tạo ra trong bước trước. Thiết lập một mật khẩu và email cho user tùy chọn và sáng tạo.

```sh
$ openstack user create tim --email tim@tim.ca \
--domain acme --description "tims openstack user account" \
--password s3cr3t
```

![scr8](http://i.imgur.com/HoHigRR.png)

**Using cURL**

- Tương tự như các lệnh cURL trước , payload có một cấu trúc phù hợp.

```sh
$ curl -s -H "X-Auth-Token: $OS_TOKEN" \
-H "Content-Type: application/json" \
-d '{ "user": { "name": "tim", "password": "s3cr3t", \
"email": "tim@tim.ca", "domain_id": \
"05a0fcbf796142bfbeb30d0fd3dfa67a", "description": \
"tims openstack user account"}}' http://localhost:5000/v3/users \
| python -mjson.tool
{
"user": {
"description": "tims openstack user account",
"domain_id": "05a0fcbf796142bfbeb30d0fd3dfa67a",
"email": "tim@tim.ca",
"enabled": true,
"id": "6eced805da4649198dd1dd3bdddadd68",
"links": {
"self": "http://localhost:5000/v3/users/6eced805da4649198dd...dadd68"
},
"name": "tim"
}
}
```

####2.10. Assigning a Role to a User for a Project.

**Using OpenStack client**

- Để gán vai trò cho một người dùng mới trên moojy dự án mới chúng ta có thể sử dụng CLI, nhưng cả 2 : người sử dụng và project phải đủ điều kiện với các Domain đúng hoặc OpenStack sẽ sử dụng domain mặc định. 

```sh
$ openstack role add member --project tims_project --project-domain acme \
--user tim --user-domain acme
```

**Using cURL**

- Các API cho việc gán vai trò khác với các lệnh trước đó, nó sử dụng PUT thay vì POST , và chỉ chấp nhận ID của user, project và role.

```sh
$ curl -s -X PUT -H "X-Auth-Token: $OS_TOKEN" \
http://localhost:5000/v3/projects/26436cea94ac4a40b890f05434f15aa4/users/6ece
d805da4649198dd1dd3bdddadd68/roles/40df936c87824fd98b7e39da44f2dbde
```

####2.11. Authenticating as the New User.

**Using OpenStack client**

- Để xác nhận như là người dùng mới, cách tốt nhất là khởi động một phiên máy trạm mới và tạo các biến môi trường mới. Trong trường hợp này tên người dùng, mật khẩu, project và thông tin về domain toàn bộ đều phải thiết lập cho phù hợp.


```sh
$ export OS_PASSWORD=s3cr3t
$ export OS_IDENTITY_API_VERSION=3
$ export OS_AUTH_URL=http://10.0.2.15:5000/v3
$ export OS_USERNAME=tim
$ export OS_PROJECT_NAME=tims_project
$ export OS_USER_DOMAIN_NAME=acme
$ export OS_PROJECT_DOMAIN_NAME=acme
```

Một khi đã được thiết lập, chúng ta có thể cố gắng tìm một token đẩm bảo cho người dùng là chúng ta có thể xác thực. 

```sh
$ openstack token issue
```

![scr9](http://i.imgur.com/O0d3nww.png)

**Using cURL**

- Tương tự như yêu cầu xác thực ("Getting a token") , cấu trúc của payload còn lại là gống nhau, nhưng giá trị phải cập nhật lại.


```sh
$ curl -i -H "Content-Type: application/json" -d ' "identity": {
"methods": ["password"],
"password": {
"user": {
"name": "tim",
"domain": { "name": "acme" },
"password": "s3cr3t"
}
}
},
"scope": {
"project": {
"name": "tims_project",
"domain": { "name": "acme" }
}
}
}
}' http://localhost:5000/v3/auth/tokens
HTTP/1.1 201 Created
Date: Thu, 27 Aug 2015 22:47:01 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Subject-Token: 1bce8850bc6c4b9e8e90b6822885d93c
Vary: X-Auth-Token
x-openstack-request-id: req-a71ec134-60d8-4f86-8546-00877a48151f
Content-Length: 5326
Content-Type: application/json
{"token": {"methods": ["password"], "roles": [{"id": "4
0df936c87824fd98b7e39da44f2dbde", "name": "Member"}], "expires_at": "2015-08-27T2
3:47:01.322257Z", "project": {"domain": {"id": "05a0fcbf796142bfbeb30d0fd3dfa67a"
, "name": "acme"}, "id": "26436cea94ac4a40b890f05434f15aa4", "name": "tims_projec
t"}, "catalog": [{"endpoints": [{"region_id": "RegionOne", "url": "http://10.0.2.
15:35357/v2.0", "region": "RegionOne", "interface": "admin", "id": "19dfab5f51b24
dbe8cfc0e7d5f4677a7"}, {"region_id": "RegionOne", "url": "http://10.0.2.15:5000/v
2.0", "region": "RegionOne", "interface": "internal", "id": "763fef84e9bd43b58733
813d5b1f29bb"}, {"region_id": "RegionOne", "url": "http://10.0.2.15:5000/v2.0", "
region": "RegionOne", "interface": "public", "id": "d210b05177b24733b40146f7dcafd
b90"}], "type": "identity", "id": "8ae94fc3bb534ef2ae5bc16979e28eaf", "name": "ke
ystone"}], "extras": {}, "user": {"domain": {"id": "05a0fcbf796142bfbeb30d0fd3dfa
67a", "name": "acme"}, "id": "6eced805da4649198dd1dd3bdddadd68", "name": "tim"},
"audit_ids": ["rWvgwqBWSjq3sZgGWFNmVg"], "issued_at": "2015-08-27T22:47:01.322285
Z"}}
```

- Có rất nhiều lệnh để người sử dụng hoặc lập trình viên OpenStack có thể thực hiện , chúng ta có thể sử dụng OpenStack --help hoặc OpenStack client's doucumention để biết thêm chi tiết.


###3. Basic Keystone Operations Using Horizon.
####3.1. What Keystone Operations Are Available through Horizon?

- Có những hoạt động khác nhau của Keystone được hỗ trợ thông qua Horizon, OpenStack's Dashboard. Tuy nhiên, tùy thuộc vào phiên bản kích hoạt trong cấu hình của Horizon file, sẽ có một số khác biệt.
- Nếu v2 Identity API được cho phép, khi đó User duy nhất và sự hỗ trợ của CRUD project là có sẵn và người dùng chỉ có khả năng chứng thực lại một domain duy nhất.
- Nếu v3 của Identity API được cho phép. Khi đó User, Group, Project, Domain và sự hỗ trợ của CRUD Project là sẵn có và người sử dụng có khả năng chứng thực lại nhiều doamains.

####3.2. Accessing the Identity Operations.

- Sự thao tác Identity sẽ xuất hiện trong menu riêng của chúng tại accordion bên phía tay trái.

![scr10](http://i.imgur.com/ayMtUQb.png)

####3.3. List, Set, Delete, Create, and View a Project.

- Từ Horizon, một người dùng sẽ có khả năng thao tác dòng lệnh dựa trên các dự án thực tế.

![scr11](http://i.imgur.com/vbp5Zya.png)

####3.4. List, Set, Delete, Create, and View a User.

- Từ Horizon, một user sẽ có khả năng thực hiện các thao tác dòng lệnh dựa trên người dùng. 

![scr12](http://i.imgur.com/PBgSAzk.png)

##Token Fomart.

###1. Lịch sử của Keystone token formats.

- Keystone cung cấp một vài dạng thành phần và người dùng tự hỏi tại sao lại có nhiều thành phần như thế. Để trả lời câu hỏi đó chúng ta cùng đi vào lịch sử phát triển của Keystone.
- Trong những ngày đầu tiên Keystone hỗ trợ một thành phần UUID. Thành phần này là thành phần gồm một chuỗi kí tự 32-bit dành cho xác thực và ủy quyền. Lợi thế của UUID là là nhỏ và rất dễ dàng sử dụng và nó là đủ đơn giản để thêm vào một lệnh cURL. Nhược điểm của thành phần này là không mang đủ tính chất của local để ủy quyền.
- Các dịch vụ của OpenStack luôn gửi trở lại các token đến máy chủ Keystone server xác định nếu một thao tác đã được cho phép. Điều này là do trong OpenStack, Keystone luôn được ping đối với bất kỳ hành động nào của OpenStack và trở thành nút cổ chai cho tất cả các dịch vụ OpenStack.
- Trong một nỗ lực để giải quyết vấn đề gặp phải với UUID, Keystone đã tạo ra một định dạng token mới đó là PKI token. Token này chứa đủ thông tin để thực hiện ủy quyền cục bộ cũng như chứa hàng loạt những dịch vụ trong token là tốt. Ngoài ra các token đã được kí kết và dịch vụ có thể cache token và sử dụng nó cho đến khi nó đã hết hạn hoặc bị thu hồi. Các PKI token dẫn đến lưu lượng ít hơn đến máy chủ Keystone, nhưng bất lợi đó là các thành phần PKI có thể dễ dàng đặt được 8K kích thước điều này làm chúng khó khăn trong các HTTP header. Ngoài ra các token gặp nhiều khó khăn hơn để sử dụng các lệnh cURl và sự trải nghiệm khi cung cấp cho những người dùng khó khăn.
- Đội Keystone đã có biến thể token này thành dạng nén, định dạng token này là PKIz, thật không may là phản hồi từ cộng đồng OpenStack về token này vẫn còn quá lớn.
- Đội Keystone đã trở lại và đưa ra một token mới, định dạng này được gọi là token Fernet. Các token Fernet khá là nhỏ (khoảng 255 kí tự) nhưng chứa thông tin đầy đủ trong đó có ủy quyền cục bộ. Các token đã có một lợi thế quan trọng. Nó chứa đầy đủ thông tin mà Keystone không có để lưu trữ dữ liệu các token thông báo trong một cơ sở dữ liệu. 
-  Như thể hiện trong biểu đồ dưới đây, token UUID hiện là các định dạng phổ biến nhất. Xin lưu ý, tuy nhiên, cuộc khảo sát này được thực hiện trong OpenStack Juno khung thời gian phát hành, và không bao gồm token Fernet vì họ không được thêm vào cho đến khi phát hành Kilo.

![scr13](http://i.imgur.com/MA5Cyd0.png)


Trong chương này, chúng tôi mô tả Keystone' UUID, PKI, và thẻ Fernet chi tiết hơn để cung cấp cho người đọc một sự hiểu biết tốt hơn về các định dạng mã thông báo rằng là có sẵn cho họ ở Keystone. Đối với tất cả ba định dạng thẻ, điều quan trọng là cần lưu ý rằng tất cả đều là thẻ ghi tên. Điều này có nghĩa là tất cả ba thẻ cần được bảo vệ không bị tiết lộ không cần thiết để ngăn chặn truy cập trái phép.


####2. UUID token.

- Keystone token đầu tiên là UUID. Định dạng UUID token chỉ đơn giản là một chuỗi kí tự ngẫu nhiên gồm 32 kí tự. Chúng được phát hành và xác nhận bởi Identity service.
- Một hexdigest () phương pháp được sử dụng đảm bảo các token tạo thành chỉ số hex.Điều này làm cho các token URL dễ dàng và an toàn hơn trong khi vận chuyển trong môi trường không nhị phân. Một token UUID được lưu trong per-sistent backend (thường là một cơ sở dữ liệu ) để có sẵn xác nhận sau này.
- Một thẻ UUID có thể bị thu hồi chỉ đơn giản bằng một yêu cầu "DELETE" với ID token. Lưu ý rằng các token thông báo không thực sự bị loại bỏ từ backend mà là đánh dấu là bị thu hồi. Kể từ khi các token thông báo là chỉ có 32 kí tự, kích thước của nó trong một HTTP header là 32 kí tự.
- Một UUID điển hình sẽ như sau : 

```sh
468da447bd1c4821bbc5def0498fd441
```

- Token rất là nhỏ và dễ sử dụng khi truy cập Keystone thông qua một cURL command. Như đã đề cập trước đó bất lợi của định dạng token này là Keystone có thể trở thành một nút cổ chai do số lượng to lớn của cộng đồng xảy ra khi Keystone cần để xác nhận token.
- Các phương pháp để trở về một token UUID là tương đối đơn giản : 

```sh
def _get_token_id(self, token_data):
return uuid.uuid4().hex
```

####3.3. PKI token.

- Định dạng được hỗ trợ thứ 2 trong Keystone là định dạng token PKI. Trong các định dạng token PKI các phản hồi được được xác nhận toàn bộ từ Keystone. Điều này có nghĩa là các dấu hiệu có một số lượng các thông tin trong đó chẳng hạn như : Khi nó nhận được phát hành, khi nó nhận được hết hạn, nhận dạng người sử dụng, project, domain và role thông tin cho người dùng này, một danh mục dịch vụ và các thông tin khác. Tất cả điều này, thông tin được đại diện trong một payload của một tài liệu JSON, và một payload sau đó được signed sử dụng cú pháp nhắn tin mã hóa CMS . Với định dạng PKIz signed sau đó payload được nén sử dụng nén zlib.
- Một ví dụ về JSON token payload : 

![scr14](http://i.imgur.com/dpKmf0Y.png)

- Như thể hiện trong Json trên, token có tất cả mọi thứ  trong một dịch vụ có quan hệ với user, domain, project và xác định thông tin các vai trò nếu user được ủy quyền cho một hoạt động dịch vụ OpenStack cụ thể.
- Dịch vụ này có thể có bộ nhớ cache của token này và đưa ra quyết định ủy quyền cục bộ mà không cần liên hệ với Keystone server trên mọi yêu cầu ủy quyền.
- Để vận chuyển token qua HTTP, 
- Một ví dụ về token trong vận chuyển : 

```sh
MIIDsAYCCAokGCSqGSIb3DQEHAaCCAnoEggJ2ew0KICAgICJhY2QogICAgICAgI...EBMFwwVzELMAkGA
1UEBhMCVVMxDjAMBgNVBAgTBVVuc2V0MCoIIDoTCCA50CAQExCTAHBgUrDgMQ4wDAYDVQQHEwVVbnNldD
EOMAwGA1UEChM7r0iosFscpnfCuc8jGMobyfApz/dZqJnsk4lt1ahlNTpXQeVFxNK/ydKL+tzEjg
```

- Chú ý rằng thậm chí một token cơ bản của một enpoint duy nhất trong catalog gần đạt tới kích cỡ xấp xỉ 1700 bytes. Với sự triển khai lớn của một vài enpoint và dịch vụ , kích cỡ một token PKI nhanh chóng vượt quá giới hạn của HTTP header mặc định cho tất cả web server (8KB). Thậm chí khi nén kích cỡ các token PKIz là không đủ để giảm bớt vấn đề này . Trong thực tế bởi token mà ra giảm thiểu nhỏ hơn 10%.
- Mặc dù PKI và PKIz token có thể cached, chúng có một vài nhược điểm. Nó có thể gây khó khăn khi cấu hình Keystone sử dụng chứng chỉ đã công bố bởi một chứng chỉ ủy quyền tin cậy. Hơn nữa, chúng có kích thước vô cùng lớn có thể dẫn đến các dịch vụ OpenStack khác có vấn đề. Kích thước của chúng có thể phá vỡ công cụ hiệu năng web, và chúng hoàn toàn khó khăn khi sử dụng trong một lệnh `cURL` . Ngoài ra trong thực tế dịch vụ Keystone điển hình phải duy trì những token này trong persistence backend dành cho các mục đích ví dụ như tạo một danh sách hủy bỏ. Kết quả là người dùng tiếp tục lo lắng về việc mất Keystone token database thường xuyên hoặc hiệu năng sẽ chịu đựng.

####3.4. Fernet token. 

- Dạng token Keystone mới nhất là token Fernet. Các token Fernet nỗ lực cải thiện các dạng token bằng hàng loạt cách . Đầu tiên, nó rất là nhỏ, nó thông thường chỉ tầm 255 kí tự và như vậy là nó lớn hơn các token của UUID, nhưng nhỏ hơn quan trọng hơn token PKI. Token chứa đầy đủ thông tin cho phép token không cần lưu trữ trong persistent Keystone token database. Thay vào đó các token có đủ thông tin mà phần còn lại cần thông tin như các vai trò của người dùng trên một dự án có thể được tạo ra. Trong một quy mô lớn phát triển OpenStack , cần phải lưu trữ một lượng lớn các token data đã được xác định là góp phần cho sự xuống cấp của hiệu năng , không đề cập đến sự cần thiết để không bị đánh mất persistent token database.
- Các token Fernet chứa một lượng thông tin nhỏ, như một identifier user, một identifier project, thông tin token hết hiệu lực , và thông tin kiểm toán khác. Các token Fernet được signed việc sử dụng một khóa đối xứng đến ngăn chặn giả mạo. Sử dụng một token Fernet giống như quy trình làm việc như là token UUID. Như vậy trái ngược với một PKI token, chúng phải được xác nhận bởi Keystone tương tự như cách này phải thực hiện với UUID token.
- Một yếu tố phức tạp khác là Fernet token sử dụng keys đối xứng để sign được token, và các keys này cần phải được phân phối cho các khu vực khác nhau của OpenStack. Ngoài ra các keys này cần phải được luân chuyển. Bởi vì Fernet token đang được sửa đổi trong thời điểm phát hành.
- Keystone cung cấp một công cụ cấu hình cho thiết lập các keys mã hóa đối xứng . Điều này có thể thực hiện bằng lệnh sau đây : 

```sh
$ keystone-manage fernet_setup
```

Ngoài ra các keys nên được luân chuyển bằng cách sử dụng câu lệnh sau :

```sh
$ keystone-manage fernet_rotate
``` 

- Như một minh họa các khía cạnh quan trọng của việc tạo ra một prject có phạm vi Fernet token được thể hiện ở phía dưới . Ví dụ lệch hướng khởi mã nguồn để cải thiện khả năng đọc. Các bước cơ bản để tạo một Fernet token là chuyển đổi một cách an toàn bất kỳ dữ liệu nào từ byte, sau đó các MessagePack chứa nội dung token, sign nó, và bảo mật URL của nó.

![scr1]()

Một Fernet token điển hình giống như :

```sh
gAAAAABU7roWGiCuOvgFcckec-0ytpGnMZDBLG9hA7Hr9qfvdZDHjsak39YN98HXxoYLIqVm19Egku5YR
3wyI7heVrOmPNEtmr-fIM1rtahudEdEAPM4HCiMrBmiA1Lw6SU8jc2rPLC7FK7nBCia_BGhG17NVHuQu0
S7waA306jyKNhHwUnpsBQ%3D
```

- Fernet token được xác nhận bằng cách thực hiện bằng cách tương tự để tạo ra token. Nhưng ngược lại, chúng ta bắt đầu bằng cách unquoting các bit URL an toàn, giải mã các giá trị và giải nén các payload. Từ đó nó chỉ đơn giản là vấn đề kiểm tra các phần khác nhau của payload đặc biệt là trường `expries_at`

```sh
def validate_token(self, token):
token = urllib.parse.unquote(six.binary_type(token))
serialized_payload = self.crypto.decrypt(token)
payload = msgpack.unpackb(serialized_payload)
user_id = cls.attempt_convert_uuid_bytes_to_hex(payload[0])
methods = auth_plugins.convert_integer_to_method_list(payload[1])
project_id = cls.attempt_convert_uuid_bytes_to_hex(payload[2])
expires_at_str = cls._convert_int_to_time_string(payload[3])
audit_ids = list(map(provider.base64_encode, payload[4]))
return (user_id, methods, project_id, expires_at_str, audit_ids)
```

##LDAP.

- Giống như OpenStack đã phát triển và bắt đầu thu thập sự chú ý từ doanh nghiệp . Một trong những yêu cầu ban đầu thông dụng nhất xung quanh khoảng trống Identity là dành cho LDAP hỗ trợ. Đây là giới thiệu đầu tiên trong một hình thức cơ bản , trong bản Essex. Trong chương này chúng ta sẽ thảo luận các phương pháp để tiếp cận và hội nhập LDAP, mới, toàn diện, các tùy chọn cấu hình dành cho LDAP, sử dụng các multiple domain cho identity và cuối cùng là các thủ thuật , các lỗi thông dụng và việc xử lý sự cố.

###1. Phương pháp tiếp cận tích hợp LDAP.

- LDAP và Microsoft's Active Directory (AD) là hai trong số những danh bạ phổ biến hiện nay tồn tại trong doanh nghiệp và được sử dụng trong nhiều công ty. Cung cấp cho doanh nghiệp đã sử dụng giống như một cửa hàng nhận dạng. Nó không đáng mong muốn và cũng không thực tế dành cho việc sao lưu và nhân rộng trong Keystone. Thay vì chúng ta muốn Keystone's Identity API là một diện mạo các thư mục của công ty , cho phép các thông tin của họ được lưu trữ và truy cập tại chỗ. Keystone được thiết kế để cung cấp loại hình này hỗ trợ bởi chính cấu trúc tự nhiên của nó. Về bản chất nó có trình điều khiển cho các backend khác công nghệ có thể được nạp vào cho phép các thành phần của Keystone để được hỗ trợ bởi lưu trữ dữ liệu bên ngoài  (như LDAP).
- Mặc dù khái niệm thì tất cả đều có thể điều khiển LDAP nhưng trên thực tế chỉ có 3 :
 <ul>
 <li>Identity : Lưu trữ User và Group.</li>
 <li>Resources : Lưu trữ Domains và Projects</li>
 <li>Assignment : Lưu trữ Roles và các Assignment của chúng.</li>
 </ul>

- Trong hầu hết các cài đặt, chỉ nhận dạng được ánh xạ tới LDAP và thường là trong chế độ chỉ đọc, vì hầu hết các doanh nghiệp có quản lý cũng như thành lập riêng biệt hệ thống thư mục của công ty họ. mapping của resources và assignment backend từ LDAP thường có vấn đề. Ví dụ như : không có những giản đồ LDAP phù hợp mà cần thiết bởi Keystone. Trong thực tế, với những vấn đề này , và rất ít các công ty cố gắng sử dụng những trình điều khiển , cả Resources và Assignment hỗ trợ cho LDAP bây giờ bị phản đối ở Keystone , và sự hỗ trợ được lên kế hoạch loại bỏ trong Mitaka phát hành của OpenStack. Vì điều này bạn được khuyến các rằng LDAP không sử dụng cho các trình điều khiển và các doanh nghiệp chỉ map Keystone Identity backend từ LDAP.

###2. Cấu hình Keystone để tích hợp với LDAP.

- Trong hình thức cơ bản của nó , Keystone LDAP được đưa vào OpenStack từ phát hành Essex. Bằng cách cung cấp tùy chọ LDAP cụ thể của bạn trong cấu hình Keystone file (keystone.conf). Keystone sẽ dịch bất kỳ người dùng và nhóm API nào gọi vào một cách gì đó trong máy chủ LDAP sẽ hiểu.
- Có một hạn chế rất lớn trong hỗ trợ LDAP cổ điển. Bạn chỉ có thể có một domain trong Keystone đó là domain mặc định. CHúng ta sẽ sẽ thảo luận về về cách chúng ta sử dụng nhiều domain trong hỗ trợ LDAP mới hơn (được giới thiệu trong Juno) sau này trong chương trình, nhưng nó vẫn có giá trị đề xem xét thông qua các hỗ trợ cổ điển , vì đây là nền tảng mà trên đó cải tiến tiếp theo trên đó được xây dựng . Hơn nữa có một vài cài đặt mà một domian duy nhất là đủ.
- Khi nhìn vào nhiệm vụ mapping người dùng Keystone và các nhóm để LDAP, trước tiên hãy kiểm tra những gì mà Keystone (và các phần còn lại của OpenStack) cần. Một User ở Keystone (được trả về bởi GET / người dùng gọi) như sau : 

```sh
{
"domain_id": "default",
"enabled": true,
"id": "ef7ab1f3fe2745fd91b3d62fd3b7309b",
"links": {
"self": "http://localhost:5000/v3/users/ef7ab1f3fe2745fd91b3d62fd3b7309b"
},
"name": "GeorgeSmiley"
}
```

- Bây giờ các liên kết thuộc tính là thực sự được điền bởi Keystone như là một phần của định dạng thực thể và như mô tả ở trên , trong LDAP classic họ chỉ hỗ trợ domain mặc định, nên dễ dàng ở bit đó. Tuy nhiên các thuộc tính khác cần phải đến từ các cửa hàng Identity (có thể là SQL, LDAP hoặc bất cứ điều gì.) - và trong thực tế có thêm một mật khẩu để bảo mật. Lý do, điều này không được trả lại bởi Keystone API nhưng nó cần phải được truy cập Keystone để thực hiện một yêu cầu xác thực. Vì thế đó là công việc của họ là hỗ trợ classic LDAP bằng cách nào đó các các map được kích hoạt ID , tên và mật khẩu cho một số các thuộc tính được lưu trong một CSDL LDAP hiện có . Hơn nữa, kể từ đó khách hàng đã có các quy trình cho việc vô hiệu hóa một người dùng trong thư mục của công ty, làm thế nào mà họ có thể đảm bảo một người dùng được vô hiệu hóa trong Keystone? và tất nhiên có một thiết lập tương tự các thuộc tính cho các nhóm thực thể Keystone là tốt, mà cũng cần phải được ánh xạ : 

```sh
{
"description": "A group of spies",
"domain_id": "default",
"id": " a2ffab1b3fe3745fdaab3d62fd3b75093",
"links": {
"self": "http://localhost:5000/v3/users/a2ffab1b3fe3745fd...d62fd3b75093"
},
"name": "Undercover"
}
```

- Giải quyết những vấn đề này. Keystone cung cấp một tập hợp các tùy chọn cấu hình mà bạn chỉ định cho mỗi thực thể Keystone, cần thuộc tính where trong trong lược đồ LDAP bạn sẽ tìm thấy tương đương. Ví dụ, đối với các đối tượng người dùng, tập tin đối tượng bao gồm những điều sau đây :

```sh
user_id_attribute = mail
user_name_attribute = mail
user_enabled_attribute = enabled
user_pass_attribute = userPassword
```

- Bên phái tay trái chỉ định thực thể trong Keystone quy cho, bên tay phải là tên thuộc tính LDAP đến việc sử dụng . Một cách tương tự cho nhóm :

```sh
group_id_attribute = cn
group_name_attribute = cn
group_desc_attribute = description
```
