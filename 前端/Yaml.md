import yaml

# 载入YAML数据
data = """
a_sequence:
  - 项目 1
  - 项目 2
  - 0.5
  - 项目 4
  - key: value
    another_key: another_value
  - - 这是一个序列
    - 在另一个序列中
  - - - 嵌套序列指示符
      - 可以折叠
      """

parsed_data = yaml.safe_load(data)

# 访问每个元素
print(parsed_data['a_sequence'][0])  # 输出 "项目 1"
print(parsed_data['a_sequence'][1])  # 输出 "项目 2"
print(parsed_data['a_sequence'][2])  # 输出 0.5
print(parsed_data['a_sequence'][3])  # 输出 "项目 4"
print(parsed_data['a_sequence'][4])  # 输出 { 'key': 'value', 'another_key': 'another_value' }
print(parsed_data['a_sequence'][5])  # 输出 ['这是一个序列', '在另一个序列中']
print(parsed_data['a_sequence'][6])  # 输出 [['嵌套序列指示符', '可以折叠']]

<img src="D:\Web学习\前端\assets\image-20241229153945820.png" alt="image-20241229153945820" style="zoom:67%;" />