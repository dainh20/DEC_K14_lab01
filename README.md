# DEC_K14_lab01
import os
import pandas as pd

# Đường dẫn thư mục chứa file JSON
path = "/Users/nguyenhuudai/coding/DEC_K14/lab_01/result_project02/products_"

# Tạo DataFrame rỗng
df = pd.DataFrame()

# Danh sách lưu các file không đọc được
list_file_json_non_download = []

for i in range(200):  
    path_file_json = f"{path}{i}.json"
    
    # Kiểm tra file có tồn tại không
    if not os.path.exists(path_file_json):
        print(f"🚫 Không tìm thấy: {path_file_json}")
        list_file_json_non_download.append(i)
        continue  

    try:
        df_json = pd.read_json(path_file_json)
        df = pd.concat([df, df_json], ignore_index=True)
    except Exception as e:
        print(f"❌ Lỗi ở file {i}: {e}")
        list_file_json_non_download.append(i)

print("\nDanh sách file lỗi hoặc không tồn tại:", list_file_json_non_download)

#Data cleaning
df["description"] = df["description"].astype(str)
df["images"] = df["images"].astype(str)


import re
df["description"] = df["description"].apply(lambda x: re.sub(r"<.*?>", "", x))
df["description"] = df["description"].apply(lambda x: x.strip())

#Load vào database
from sqlalchemy import create_engine
engine = create_engine('postgresql://nguyenhuudai:zxcvbnm@localhost:5432/dec_k14')

df.to_sql('lab01', con=engine, if_exists='replace', index=False)
