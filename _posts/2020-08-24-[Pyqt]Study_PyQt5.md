---
tags: pyqt python gui
toc: True
---
# PyQt5 정리

## Event 만들기

### Qt Desinger에서 
   1. Qt Designer에서 Click 버튼을 생성한다.
   2. 안으로 붙이기를 하고 버튼을 끌어서 event를 생성하는 칸으로 들어간다.
   3. 내가 만들고 싶은 event 이름을 만든 후  그 이름으로 저장해준다.
   4. 만들었던 event 이름과 class 내부의 함수의 이름이 같아야한다. 
   5. def의 이름을 event 이름으로 만들어 준 이후 그 밑에 이벤트가 클릭 되었을 때 실행되는 현상들을 적어주자.
   
### Python code 내부에서 
   1. Qt Designer에서 Click 버튼을 생성한다.
   2. 그 버튼의 이름을 잘 기억해두고 class init부분으로 이동한다.
   3. connect 함수를 이용한다.
   4. self.(click_event_obj_name).connect(self.이벤트함수)
   5. 이벤트 함수 안에 발생했으면 하는 event를 만든다.
   
## 값 삽입하기

### self.function_name.setText
   
   * 값을 설정한다.
   
### self.function_name.insertPlainText

   * 값을 이어 붙여서 설정한다.
   
### self.function_name.setStyleSheet("Color: red")

   * 값의 색깔을 설정한다.     
 
