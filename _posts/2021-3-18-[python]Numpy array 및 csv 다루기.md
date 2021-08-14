---
tags: python data-preprocessng
toc: True
---
# np.ones(num)
num의 수만큼의 가로의 행열을 생성한다( 값은 1로 )
[1,1,1,1,1,1,...]
--> np.zeros(num) 하면 값은 0으로 생성가능
(num,1)

```python
pid_label = np.ones(21)
```

# Transpose
배열의 행열을 바꾼다.
A = A.T

# np.delete
```python
np.delete(지우고싶은 array, 지우려는 행또는 열, axis=행(0)열(1))
```

# np.concetenate(a,b)
a와 b array를 합친다. 옆으로 이어붙인다. 


# np.hstatck
열끼리 합친다.

# np.nan_to_num
array에 있는 nan 값을 0 으로 모두 바꾼다.
```python
np.nan_to_num(var, copy=False)
```

# np.arrange(num)
num의 수까지 차례대로 증가하는 array를 만든다.
[1,2,3,4,5,6,...,num]

# np.reshape(-1,1)
가로로 되어있는 것을 세로로 만들 수 있다.
(1,num)

```python
pid_label = pid_label.reshape(-1, 1)
```

# csv2numpy
```python
test_data = genfromtxt('test_log.csv', delimiter=',')
```

# dic2csv
* target dic file name = waves_peak
* output csv file name = test_log.csv

```python
with open("test_log.csv", 'w') as outfile:
    csv_writer = csv.writer(outfile, delimiter=',', quotechar='|', quoting=csv.QUOTE_MINIMAL)
    for k, v in waves_peak.items():
        csv_writer.writerow([k] + v)

```

# list2csv
list가 2D라면 writer.writerows를 써주고 
1D라면 writer.writerow를 써준다.
* target list name = final list
* label list name = x_label
* output csv file name = final_output.csv

```python
with open('final_output.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(x_label)
    writer.writerows(final_list)
```



# Entirety code
```python
# Make CSV file
with open("test_log.csv", 'w') as outfile:
    csv_writer = csv.writer(outfile, delimiter=',', quotechar='|', quoting=csv.QUOTE_MINIMAL)
    for k, v in waves_peak.items():
        csv_writer.writerow([k] + v)

# Modify csv by making to numpy
test_data = genfromtxt('test_log.csv', delimiter=',')
test_data = test_data.T
np.nan_to_num(test_data, copy=False)
test_data = np.delete(test_data, 0, axis=0)

# Set Pid
pid_label = np.ones(13)
pid_label = pid_label.reshape(-1, 1)

# Set x label
x_label = ['Pid', 'ECG_P_Peaks', 'ECG_Q_Peaks'
    , 'ECG_R_Peaks', 'ECG_S_Peaks', 'ECG_T_Peaks'
    , 'ECG_P_Onsets', 'ECG_T_Offsets']

# Concatenate
test_data = np.hstack((pid_label, test_data))
test_data_to_int = test_data.astype(np.int64)
rpeaks_item = rpeaks['ECG_R_Peaks']
final_data = np.insert(test_data_to_int, 3, rpeaks_item, axis=1)
final_x_list = final_data.tolist()

with open('final_output_x.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(x_label)
    writer.writerows(final_x_list)

# Find y value from x value
for i, each_row in enumerate(final_x_list):
    for j, each_value in enumerate(each_row):
        if each_value == 1:
            pass
        else:
            final_x_list[i][j] = ecg_signal[each_value]
final_list = final_x_list

# final_output_y.csv
with open('final_output_y.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(x_label)
    writer.writerows(final_list)

# Concatenate x and y
final_x = genfromtxt('final_output_x.csv', delimiter=',')
final_y = genfromtxt('final_output_y.csv', delimiter=',')
pre_pre_final = np.concatenate((final_y, final_x), axis=1)
pre_final = np.delete(pre_pre_final, 8, axis=1)
final = np.delete(pre_final, 0, axis=0)
final = final.astype(np.int64)
with open('final_output.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['Pid','ECG_P_Peaks', 'ECG_Q_Peaks'
                        , 'ECG_R_Peaks', 'ECG_S_Peaks', 'ECG_T_Peaks'
                        , 'ECG_P_Onsets', 'ECG_T_Offsets', 'P_amplitude',
                     'Q_amplitude', 'R_amplitude', 'S_amplitude', 'T_amplitude', 'P_onset_amplitude',
                     'T_offset_amplitude'])
    writer.writerows(final)

## by meanleess

```
