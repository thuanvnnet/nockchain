Hướng Dẫn Cài Đặt và Chạy Nockchain
Hướng dẫn này cung cấp các bước chi tiết để cài đặt và chạy Nockchain, một giao thức blockchain sử dụng Zero-Knowledge Proof of Work (zkPoW), dựa trên kho lưu trữ tại https://github.com/zorp-corp/nockchain. Hướng dẫn tập trung vào hệ điều hành Debian/Ubuntu và bao gồm các bước để thiết lập môi trường, xây dựng, chạy node, và khai thác (mining) trên testnet hoặc mainnet.
Lưu ý: Nockchain là một dự án thử nghiệm, nhiều phần chưa được kiểm toán, và không có bảo đảm về hành vi của phần mềm. Hãy thực hiện cẩn thận và đọc kỹ các cảnh báo từ nhà phát triển.
1. Yêu Cầu Hệ Thống
Phần cứng

CPU: Khuyến nghị sử dụng CPU mạnh để khai thác (mining) vì Nockchain sử dụng zkPoW, đòi hỏi khả năng tính toán cao.
RAM: Tối thiểu 8GB, khuyến nghị 16GB trở lên cho hiệu suất tốt hơn.
Dung lượng lưu trữ: Tối thiểu 20GB trống để chứa mã nguồn, các file build, và dữ liệu checkpoint.
Kết nối mạng: Kết nối internet ổn định. Nếu ở phía sau tường lửa hoặc NAT, cần mở cổng P2P (xem chi tiết ở bước cấu hình).

Phần mềm

Hệ điều hành: Debian/Ubuntu (hướng dẫn này tập trung vào các hệ này).
Các công cụ cần thiết:
rustup: Công cụ để cài đặt và quản lý Rust.
clang, llvm-dev, libclang-dev: Các thư viện cần thiết để biên dịch.
git: Để clone kho lưu trữ.
make: Để chạy các lệnh build.



2. Cài Đặt Các Phụ Thuộc

Cập nhật hệ thống:
sudo apt update
sudo apt upgrade -y


Cài đặt các phụ thuộc cần thiết:
sudo apt install clang llvm-dev libclang-dev git make -y


Cài đặt Rust:

Cài đặt rustup theo hướng dẫn chính thức tại https://rustup.rs/:curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh


Sau khi cài đặt, kích hoạt Rust trong terminal:source $HOME/.cargo/env





3. Tải và Cấu Hình Nockchain

Clone kho lưu trữ Nockchain:
git clone https://github.com/zorp-corp/nockchain
cd nockchain


Sao chép file môi trường:

Sao chép file mẫu .env_example thành .env:cp .env_example .env




Cập nhật PATH:

Thêm đường dẫn Cargo vào biến môi trường PATH để chạy các lệnh Nockchain:export PATH="$HOME/.cargo/bin:$PATH"


Để PATH được lưu vĩnh viễn, thêm dòng trên vào file ~/.bashrc hoặc ~/.zshrc:echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc





4. Biên Dịch và Cài Đặt Nockchain

Cài đặt hoonc (trình biên dịch NockApp):
make install-hoonc


Biên dịch Nockchain:

Lệnh này có thể mất thời gian tùy thuộc vào phần cứng:make build




Cài đặt ví Nockchain:
make install-nockchain-wallet


Cài đặt Nockchain:
make install-nockchain



5. Tạo và Quản Lý Ví Nockchain

Tạo ví:

Tạo một ví mới để lưu trữ khóa công khai và khóa riêng tư:nockchain-wallet keygen


Lưu lại memo, khóa riêng tư, và khóa công khai được hiển thị. Đây là thông tin quan trọng để quản lý ví của bạn.


Cập nhật khóa công khai vào file .env:

Mở file .env:nano .env


Tìm dòng MINING_PUBKEY= và thay thế bằng khóa công khai của bạn.
Lưu file: Nhấn Ctrl + X, sau đó Y, và Enter.


Lưu ý:

Mỗi khi khởi động lại terminal, hãy đảm bảo chạy lại:cd nockchain
export PATH="$HOME/.cargo/bin:$PATH"


Điều này tránh lỗi wallet: command not found.



6. Chạy Một Node Nockchain
Chạy trên Testnet (Fakenet)

Tạo thư mục cho các node:
mkdir fakenet-hub fakenet-node
cp .env fakenet-hub/
cp .env fakenet-node/


Chạy hub node:
cd fakenet-hub
sh ../scripts/run_nockchain_node_fakenet.sh


Chạy miner node:

Mở một terminal mới, sau đó:cd nockchain/fakenet-node
sh ../scripts/run_nockchain_miner_fakenet.sh




Lưu ý:

Các node phải chạy trong các thư mục riêng biệt vì dữ liệu checkpoint được lưu trong thư mục làm việc.
Nếu bạn ở phía sau tường lửa hoặc NAT, cần mở cổng P2P (mặc định là cổng UDP). Ví dụ:nockchain --bind /ip4/0.0.0.0/udp/$PEER_PORT/quic-v1

Thay $PEER_PORT bằng cổng bạn chọn (ví dụ: 30333).



Chạy trên Mainnet

Chạy miner node:

Tương tự như testnet, tạo các thư mục riêng:mkdir node1 node2
cp .env node1/
cp .env node2/


Chạy từng node:cd node1
sh ../scripts/run_nockchain_miner.sh

cd node2
sh ../scripts/run_nockchain_miner.sh




Kiểm tra trạng thái khai thác:

Hiện tại, không có cách kiểm tra trạng thái khai thác hoặc chiều cao khối trực tiếp. Bạn có thể kiểm tra log để tìm các dòng như:[%mining-on ...]

hoặc:block Vo3d2Qjy1YHMoaHJBeuQMgi4Dvi3Z2GrcHNxvNYAncgzwnQYLWnGVE added to validated blocks at 2


Để lọc log, sử dụng biến RUST_LOG:RUST_LOG=info nockchain

hoặc chỉ hiển thị lỗi:RUST_LOG=error nockchain





7. Khai Thác (Mining) Nockchain

Cơ chế: Nockchain sử dụng zkPoW, yêu cầu miner tạo Zero-Knowledge Proof (ZKP) cho một bài toán cố định, sau đó băm ZKP để nhận $NOCK dựa trên sức mạnh tính toán.
Tổng cung: 2^32 $NOCK (khoảng 4.29 tỷ), được phân phối hoàn toàn cho miner thông qua fair launch (không có pre-mine).
Lưu ý về khai thác:
Khai thác yêu cầu phần cứng mạnh (CPU-based). Bạn có thể chạy solo hoặc tham gia pool (tương tự Bitcoin).
Hiện tại, chỉ có phương pháp CLI được cung cấp, chưa có GUI hoặc pool chính thức.
Zorp đã bị chỉ trích vì cung cấp phiên bản khai thác tối ưu cho nhóm của họ, dẫn đến khó khăn cho cộng đồng trong việc khai thác khối. Hãy cân nhắc đầu tư phần cứng và tối ưu hóa thuật toán nếu tham gia.



8. Các Lệnh Hữu Ích Khác

Kiểm tra ghi chú ví:
cd ~/nockchain/node1
nockchain-wallet --nockchain-socket .socket/nockchain_npc.sock list-notes


Dừng miner:
screen -XS miner quit


Xóa Nockchain:
rm -rf nockchain
rm -rf .nockapp



9. Lưu Ý và Cảnh Báo

Tính thử nghiệm: Nockchain là phần mềm thử nghiệm, có thể có thay đổi phá vỡ đồng thuận trước khi ra mắt mainnet. Theo dõi kho lưu trữ để cập nhật.
Tường lửa/NAT: Nếu bạn ở phía sau NAT, cần chuyển tiếp cổng (port forwarding). Tham khảo hướng dẫn từ nhà cung cấp router hoặc sử dụng công cụ như ChatGPT để thiết lập.
Hiệu suất khai thác: Zorp đã bị chỉ trích vì giữ lợi thế khai thác, khiến cộng đồng khó cạnh tranh. Hãy chuẩn bị phần cứng mạnh và tối ưu hóa thuật toán nếu muốn tham gia khai thác.
Bảo mật ví: Lưu trữ khóa riêng tư và memo an toàn. Mất khóa sẽ dẫn đến mất quyền truy cập ví.

10. Tài Nguyên Tham Khảo

Kho lưu trữ GitHub: https://github.com/zorp-corp/nockchain
Trang web chính thức: https://nockchain.org
Zorp: https://zorp.io
Diễn đàn Nockchain: https://forum.nockchain.org
Rustup: https://rustup.rs
