# Tcpdump

**Chụp gói tin cơ bản**

* **tcpdump -i INTERFACE** : 	Bắt các gói tin trên một giao diện mạng cụ thể
* **tcpdump -w FILE** : Ghi các gói tin đã chụp vào một tệp
* **tcpdump -r FILE** : Đọc các gói tin đã chụp từ một tệp
* **tcpdump -c COUNT** : Chụp một số lượng gói tin cụ thể
* **tcpdump -n** : Không giải quyết địa chỉ IP
* **tcpdump -nn** : Không giải quyết địa chỉ IP và không giải quyết số giao thức
* **tcpdump -v** : Hiển thị chi tiết; mức độ chi tiết có thể được tăng lên với -vv và -vvv



Ví dụ sau:

* tcpdump -i eth0 -c 50 -v thu thập và hiển thị 50 gói tin bằng cách lắng nghe trên eth0giao diện là Ethernet có dây và hiển thị chúng một cách chi tiết.
* tcpdump -i wlo1 -w data.pcap bắt các gói tin bằng cách lắng nghe trên wlo1giao diện (giao diện WiFi) và ghi các gói tin vào data.pcap. Quá trình này sẽ tiếp tục cho đến khi người dùng ngắt quá trình bắt bằng cách nhấn CTRL-C.
* tcpdump -i any -nn thu thập các gói tin trên tất cả các giao diện và hiển thị chúng trên màn hình mà không cần phân giải tên miền hoặc giao thức.



**Lọc biểu thức**

* **tcpdump host IP** hoặc **tcpdump host HOSTNAME** : Lọc các gói tin theo địa chỉ IP hoặc tên máy chủ
* **tcpdump src host IP** : Lọc các gói tin theo máy chủ nguồn cụ thể
* **tcpdump dst host IP** : Lọc các gói tin theo máy chủ đích cụ thể
* **tcpdump port PORT\_NUMBER** : Lọc các gói tin theo số cổng
* **tcpdump src port PORT\_NUMBER** : Lọc các gói tin theo số cổng nguồn được chỉ định
* **tcpdump dst port PORT\_NUMBER** : Lọc các gói tin theo số cổng đích đã chỉ định
* **tcpdump PROTOCOL** : Lọc các gói tin theo giao thức; ví dụ bao gồm ip, ip6, và icmp



Các ví dụ sau:

* tcpdump -i any tcp port 22 lắng nghe trên tất cả các giao diện và bắt tcp các gói tin đến hoặc đi port 22, tức là lưu lượng SSH .
* tcpdump -i wlo1 udp port 123lắng nghe trên card mạng WiFi và lọc udp lưu lượng đến port 123Giao thức thời gian mạng ( NTP ).
* tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap sẽ lắng nghe trên eth0, giao diện Ethernet có dây và lọc lưu lượng được trao đổi với example.com giao diện đó sử dụng tcp và port 443. Nói cách khác, lệnh này đang lọc lưu lượng HTTPS liên quan đến example.com.

**Lọc nâng cao**

Còn rất nhiều cách khác để lọc gói tin. Xét cho cùng, trong bất kỳ tình huống thực tế nào, chúng ta cũng cần lọc qua hàng nghìn, thậm chí hàng triệu gói tin. Việc có thể biểu diễn chính xác các gói tin cần hiển thị là điều không thể thiếu.

 Ví dụ, chúng ta có thể giới hạn các gói tin được hiển thị ở mức nhỏ hơn hoặc lớn hơn một độ dài nhất định:

* **greater LENGTH**: Lọc các gói tin có độ dài lớn hơn hoặc bằng độ dài đã chỉ định

* **less LENGTH**: Lọc các gói tin có độ dài nhỏ hơn hoặc bằng độ dài đã chỉ định

**Byte tiêu đề**

Mục đích của phần này là để lọc các gói tin dựa trên nội dung của một byte tiêu đề. Hãy xem xét các giao thức sau: ARP , Ethernet, ICMP, IP, TCP và UDP . Đây chỉ là một vài giao thức mạng mà chúng ta đã nghiên cứu. Làm thế nào để yêu cầu Tcpdump lọc các gói tin dựa trên nội dung của các byte tiêu đề giao thức? 


Khi sử dụng pcap -filter, Tcpdump cho phép bạn tham chiếu đến nội dung của bất kỳ byte nào trong tiêu đề bằng cú pháp sau **proto\[expr:size]**, trong đó:

* **proto** đề cập đến giao thức. Ví dụ: arp, ether, icmp, ip, ip6, tcp, và udp đề cập đến ARP , Ethernet, ICMP, IPv4, IPv6, TCP và UDP tương ứng.

* **expr** biểu thị độ lệch byte, trong đó 0 tham chiếu đến byte đầu tiên.

* **size** cho biết số byte mà chúng ta quan tâm, có thể là một, hai hoặc bốn. Giá trị này là tùy chọn và mặc định là một.



Để hiểu rõ hơn về điều này, hãy xem xét hai ví dụ sau từ trang hướng dẫn pcap -filter (và đừng lo lắng nếu bạn thấy chúng khó):

* ether\[0] \& 1 != 0lấy byte đầu tiên trong tiêu đề Ethernet và số thập phân 1 (tức là 0000 0001ở dạng nhị phân) và áp dụng \&(phép toán nhị phân And). Bộ lọc sẽ trả về true nếu kết quả không bằng số 0 (tức là 0000 0000). Mục đích của bộ lọc này là hiển thị các gói tin được gửi đến một địa chỉ đa hướng. Địa chỉ Ethernet đa hướng là một địa chỉ cụ thể xác định một nhóm thiết bị dự định nhận cùng một dữ liệu.
* ip\[0] \& 0xf != 5lấy byte đầu tiên trong tiêu đề IP và so sánh nó với số thập lục phân F (tức là 0000 1111ở dạng nhị phân). Bộ lọc sẽ trả về true nếu kết quả không bằng số (thập phân) 5 (tức là 0000 0101ở dạng nhị phân). Mục đích của bộ lọc này là bắt tất cả các gói tin IP có tùy chọn.
* Đừng lo lắng nếu bạn thấy hai ví dụ trên phức tạp. Chúng tôi đã đưa chúng vào để bạn biết mình có thể đạt được những gì; tuy nhiên, việc hiểu đầy đủ các ví dụ trên không nhất thiết phải hoàn thành nhiệm vụ này. Thay vào đó, chúng ta sẽ tập trung vào việc lọc các gói tin TCP dựa trên các cờ TCP đã thiết lập .



Bạn có thể sử dụng tcp\[tcpflags] để tham chiếu đến trường cờ TCP . Các cờ TCP sau đây có sẵn để so sánh:

* **tcp-syn** (Đồng bộ hóa)
* **tcp-ack** (Xác nhận)
* **tcp-fin** (Kết thúc)
* **tcp-rst** (Đặt lại)
* **tcp-push**



Dựa trên những điều trên, chúng ta có thể viết:

* tcpdump "tcp\[tcpflags] == tcp-syn" để bắt các gói tin TCP chỉ có cờ SYN (Đồng bộ hóa) được đặt, trong khi tất cả các cờ khác đều không được đặt.
* tcpdump "tcp\[tcpflags] \& tcp-syn != 0" để bắt các gói tin TCP có ít nhất cờ SYN (Đồng bộ hóa) được đặt.
* tcpdump "tcp\[tcpflags] \& (tcp-syn|tcp-ack) != 0" để nắm bắt các gói tin TCP có ít nhất cờ SYN (Đồng bộ hóa) hoặc ACK (Xác nhận) được đặt.



**Hiển thị các gói tin**

Tcpdump là một chương trình phong phú với nhiều tùy chọn để tùy chỉnh cách in và hiển thị các gói tin. Chúng tôi đã chọn đề cập đến năm tùy chọn sau:
* **tcpdump -q** : Nhanh chóng và yên tĩnh: thông tin gói ngắn gọn
* **tcpdump -e** : Bao gồm địa chỉ MAC
* **tcpdump -A** : In các gói tin dưới dạng mã hóa ASCII
* **tcpdump -xx** : Hiển thị các gói tin theo định dạng thập lục phân
* **tcpdump -X** : Hiển thị các gói tin theo cả định dạng thập lục phân và ASCII



# NMAP : THE BASICS

1. **Khám phá máy chủ: Ai đang trực tuyến**

Nmap sử dụng nhiều cách để xác định mục tiêu của mình:

* Phạm vi IP sử dụng - : Nếu bạn muốn quét tất cả các địa chỉ IP từ 192.168.0.1 đến 192.168.0.10, bạn có thể viết **192.168.0.1-10**
* Mạng con IP sử dụng /: Nếu bạn muốn quét một mạng con, bạn có thể biểu thị nó như **192.168.0.1/24**, và điều này sẽ tương đương với **192.168.0.0-255**
* Tên máy chủ: Bạn cũng có thể chỉ định mục tiêu của mình theo tên máy chủ, ví dụ : **example.thm**

Nmap cung cấp **-sn** tùy chọn quét ping.

Chúng ta có thể kiểm soát tốt hơn cách Nmap phát hiện các máy chủ trực tiếp như -PS\[portlist], -PA\[portlist], -PU\[portlist]cho TCP SYN, TCP ACK và UDP discovery thông qua các cổng được chỉ định.

Cuối cùng, Nmap cung cấp tùy chọn quét danh sách **-sL**. Tùy chọn này chỉ liệt kê các mục tiêu cần quét mà không thực sự quét chúng. Ví dụ: nmap -sL 192.168.0.1/24 sẽ liệt kê 256 mục tiêu sẽ được quét. Tùy chọn này giúp xác nhận các mục tiêu trước khi thực hiện quét.

**2. Quét cổng: Ai đang nghe lén**

**Quét cổng TCP**

* Quét kết nối có thể được kích hoạt bằng cách sử dụng **-sT**. Nó sẽ cố gắng hoàn tất quá trình bắt tay ba chiều TCP với mọi cổng TCP đích. Nếu cổng TCP mở và Nmap kết nối thành công, Nmap sẽ hủy kết nối đã thiết lập.

**Quét SYN (Ẩn)**

* Không giống như quét kết nối, cố gắng kết nối đến cổng TCP đích , tức là hoàn tất bắt tay ba chiều, quét SYN chỉ thực hiện bước đầu tiên: nó gửi một gói tin TCP SYN. ​​Do đó, bắt tay ba chiều TCP không bao giờ được hoàn tất. Ưu điểm là điều này dự kiến ​​sẽ dẫn đến ít nhật ký hơn vì kết nối không bao giờ được thiết lập, và do đó, nó được coi là một quét tương đối bí mật. Bạn có thể chọn quét SYN bằng cách sử dụng **-sS** cờ.

**Quét cổng UDP**

* Mặc dù hầu hết các dịch vụ sử dụng TCP để giao tiếp, nhiều dịch vụ khác lại sử dụng UDP . Ví dụ bao gồm DNS , DHCP , NTP (Giao thức Thời gian Mạng), SNMP (Giao thức Quản lý Mạng Đơn giản) và VoIP (Thoại qua IP). UDP không yêu cầu thiết lập và ngắt kết nối sau đó. Hơn nữa, nó rất phù hợp cho giao tiếp thời gian thực, chẳng hạn như phát sóng trực tiếp. Tất cả những điều này là lý do để cân nhắc quét và khám phá các dịch vụ đang lắng nghe trên các cổng UDP .
* Nmap cung cấp tùy chọn -sUquét các dịch vụ UDP . Vì UDP đơn giản hơn TCP , chúng tôi dự đoán lưu lượng sẽ khác nhau.

**Giới hạn các cổng mục tiêu**

Theo mặc định, Nmap quét 1.000 cổng phổ biến nhất. Tuy nhiên, đây có thể không phải là điều bạn đang tìm kiếm. Do đó, Nmap cung cấp cho bạn một vài tùy chọn khác.

* **-F** dành cho chế độ Nhanh, quét 100 cổng phổ biến nhất (thay vì 1000 cổng mặc định).
* **-p \[range]** cho phép bạn chỉ định phạm vi cổng cần quét. Ví dụ: -p10-1024 quét từ cổng 10 đến cổng 1024, trong khi -p-25 quét tất cả các cổng từ 1 đến 25. Lưu ý rằng -p- quét tất cả các cổng và tương đương với -p1-65535và là lựa chọn tốt nhất nếu bạn muốn quét càng kỹ càng tốt.

**3. Phát hiện phiên bản: Trích xuất thêm thông tin**

**Phát hiện hệ điều hành**

Bạn có thể bật tính năng phát hiện hệ điều hành bằng cách thêm **-O** tùy chọn này. Đúng như tên gọi, tùy chọn phát hiện hệ điều hành sẽ kích hoạt Nmap dựa vào các chỉ số khác nhau để đưa ra dự đoán chính xác về hệ điều hành mục tiêu .

**Phát hiện dịch vụ và phiên bản**

Bạn đã phát hiện ra một số cổng mở và muốn biết những dịch vụ nào đang lắng nghe trên các cổng đó. **-sV** Tính năng này cho phép phát hiện phiên bản. Điều này rất tiện lợi để thu thập thêm thông tin về mục tiêu của bạn mà chỉ cần ít lần nhấn phím hơn.

-> Nếu bạn có thể có cả **-O, -sV** và một số tùy chọn khác trong cùng một tùy chọn thì sao? Đó chính là **-A**. Tùy chọn này cho phép phát hiện hệ điều hành , quét phiên bản và theo dõi đường dẫn, cùng nhiều tính năng khác.

**Bắt buộc quét**

Khi chúng ta chạy quét cổng, chẳng hạn như sử dụng **-sS**, có khả năng máy chủ đích không phản hồi trong giai đoạn khám phá máy chủ (ví dụ: máy chủ không phản hồi yêu cầu ICMP). Do đó, Nmap sẽ đánh dấu máy chủ này là ngừng hoạt động và sẽ không khởi chạy quét cổng trên máy chủ đó. Chúng ta có thể yêu cầu Nmap coi tất cả máy chủ là trực tuyến và quét cổng trên mọi máy chủ, bao gồm cả những máy chủ không phản hồi trong giai đoạn khám phá máy chủ. Lựa chọn này có thể được kích hoạt bằng cách thêm **-Pn** tùy chọn.

**4. Thời gian: Nhanh thế nào là nhanh**

Nmap cung cấp nhiều tùy chọn khác nhau để kiểm soát tốc độ và thời gian quét :

* T0 (hoang tưởng)	9,8 giờ
* T1 (lén lút)	        27,53 phút
* T2 (lịch sự)	        40,56 giây
* T3 (bình thường)	0,15 giây
* T4 (hung hăng)	0,13 giây
* --min-parallelism <numprobes> Và --max-parallelism <numprobes>	Số lượng đầu dò song song tối thiểu và tối đa
* --min-rate <number>Và--max-rate <number>	                        Tốc độ tối thiểu và tối đa (gói/giây)
* --host-timeout	                                                Khoảng thời gian tối đa để chờ máy chủ đích

**5. Đầu ra: Kiểm soát những gì bạn nhìn thấy**

**Độ chi tiết và gỡ lỗi**

* Tùy chọn này rất có thể **-v** là quá đủ cho đầu ra chi tiết; tuy nhiên, nếu bạn vẫn chưa hài lòng, bạn có thể tăng mức độ chi tiết bằng cách thêm một ký tự "v" khác, chẳng hạn như -vvhoặc thậm chí **-vvvv**. Bạn cũng có thể chỉ định trực tiếp mức độ chi tiết, ví dụ, -v2 và -v4. Bạn thậm chí có thể tăng mức độ chi tiết bằng cách nhấn "v" sau khi quá trình quét đã bắt đầu.
* Nếu tất cả những điều rườm rà này không đáp ứng được nhu cầu của bạn, bạn phải cân nhắc **-d** đầu ra ở cấp độ gỡ lỗi. Tương tự, bạn có thể tăng cấp độ gỡ lỗi bằng cách thêm một hoặc nhiều ký tự "d" hoặc bằng cách chỉ định trực tiếp cấp độ gỡ lỗi. Cấp độ tối đa là **-d9**; trước khi chọn cấp độ này, hãy đảm bảo bạn đã sẵn sàng cho hàng ngàn thông tin và dòng gỡ lỗi.

**Lưu báo cáo quét**

Trong nhiều trường hợp, chúng ta cần lưu kết quả quét. Nmap cung cấp nhiều định dạng khác nhau. Ba định dạng hữu ích nhất là đầu ra bình thường (thân thiện với người dùng), đầu ra XML và đầu ra grepable, liên quan đến lệnh . grep Bạn có thể chọn định dạng báo cáo quét như sau:

* **-oN filename**- Đầu ra bình thường
* **-oX filename**- Đầu ra XML
* **-oG filename grep**- đầu ra có thể (hữu ích cho grep và awk)
* **-oA basename**- Xuất ra tất cả các định dạng chính
