# Meeting Room Reservation System

여의도 순복음 교회 제2교육관 8층 모임 장소 예약 페이지

## 프로젝트 개요

현재 우리 교회에 모임 장소를 대여하려면 회장단에게 개인적으로 문의를 해야만 가능하다.

위 불편함을 해소시키고자 시간과 장소에 따른 예약이 가능한 Web Table Page를 만들어서 제공할 계획이다.

위 기능을 구현하기 위해 현재 회사에서 사용하고 있는 회의실 예약 시스템을 참고해서 만들어볼 예정이다.

## 참고 레퍼런스
![image](https://user-images.githubusercontent.com/75019048/160243034-16562a13-1d7f-4b7d-a797-c0b4c6e4e69c.png)


## 구현 기술 스택

1. 유니티 
2. Google Spread Sheets
3. Google Sheets To Unity

## 개발 프로세스

1. 요구 사항 분석 
2. UI, Data Table 설계
3. UI 작업
4. Data Class 구현
5. Controller & Data 연동
6. UI & Controller 연동
7. 테스트 코드 구현
8. Google Spread Sheets 연동 환경 설정
9. WebGL Build 후 웹 페이지에 개시

## 가용 시간

주 1 ~ 2회로 회당 2~3시간 정도 작업을 진행할 예정

## 설계 

### UI 설계

1. **메인 Page**
    1. Bar
    2. Page 
        1. Scroll Rect(가로 세로 모두 스크롤 가능 하도록 구현
            1. Horizontal Layout Group
                1. 1열 : 시간(Vertical Layout Group)
                    1. 30분 단위로 끊어서 아이콘 생성 (10:00~ 21:00 → 시간은 변경 가능)
                2. 2열 : 장소1 (Vertical Layout Group)
                    1. (1행) Room Name 
                    2. (이후) Button Icon
                        ⇒ 해당 버튼 클릭시 장소 & 순서를 통해 예약 상세 페이지에 예약 날짜 및 장소 데이터 전달
                        
                3. 3열 : 장소2 (Vertical Layout Group)
                    1. (1행) Room Name
                    2. (이후) Button Icon
                        ⇒ 해당 버튼 클릭시 장소 & 순서를 통해 예약 상세 페이지에 예약 날짜 및 장소 데이터 전달
                4. ...
        2. 주의 사항
            1. 몇 개의 아이콘을 생성해야 하는지 예약 가능 시간을 통해 알아야 한다.

2. **예약 상세 Page**
    1. Backgroud
    2. Page
        1. Vertical Layour group
            1. 예약 text
            2. 회의실 장소명 : 예약 데이터
            3. 일자 : 날짜, 시작 ~ 끝 시간
            4. 목적
            5. 예상 인원
            6. 예약자 정보: 이름, 교구, 핸드폰

3. **예약 취소 Pop up**
    1. Page
        1. Title: “핸드폰 뒷자리 입력시 예약이 취소됩니다”
        2. Input Field: Number
        3. Buttons: 예약 취소, 닫기


## 데이터 설계

``` C#
class ReservatrionInfo : ScriptableObject
{
    bool isOccupied;
    string booker;
    string RoomName;
    DateTime Today;
    DateTime StartTime;
    DateTIme EndTime;
    string Purpose;
    int ExpectedNum;
}
```

## Page class 설계

### **MainScene.cs**

Fields

1. string Path

Methods

1. Awake()
2. LoadCanvas(string path) : void
3. LoadMainPage(string path) : void

### **MainPage.cs**

각 RoomPage를 생성

실제 각 회의실의 시간별 예약 정보는 RoomPage에서 get & set 

Fields

1. RoomPage RoomPagePrefab
2. Transform ContentParent
3. Enum RoomName {a, b, c, d ..}

Methods

1. Awake()
2. InstantiateRoomPages(string path) : void
    
    [→ Enum의 갯수만큼 생성](https://afsdzvcx123.tistory.com/entry/C-%EB%AC%B8%EB%B2%95-C-Enum-Count-%EA%B0%80%EC%A0%B8%EC%98%A4%EB%8A%94-%EB%B0%A9%EB%B2%95)
    
3. OnNextPage() : void
    
    → ShowTodayRoomPages(true)
    
4. OnPreviousPage() : void
    
    → ShowTodayRoomPages(false)
    
5. ShowTodayRoomPages(DateTime today, bool isNextPage) : void
    
    → RoomPage.ShowTodayInfo(DateTime today
    

### **RoomPage.cs**

Fields

1. Enum RoomName
2. Icon IconPrefab
3. List<ReservatrionInfo> ReservatrionInfos

Methods

1. Awake()
2. LoadReservatrionInfo() : List<ReservatrionInfo>
3. SetReservatrionInfo(List<ReservatrionInfo> info) : void
    
    → Icon.isOccupied = info.isOccupied
    

### **Icon.cs**

Fields

1. bool isOccupied
    
    → set: ShowOccupied(isOccupied)
    
2. ReservationDetailsPage page
3. ReservatrionInfo info
4. Button ReservationButton

Methods

1. ShowOccupied(bool isOccupied)
2. ShowReservationDetailsPage(ReservatrionInfo info)

### ReservationDetailsPage.cs

Fields

1. Icon Icon
2. Text RoomName
3. Text DateTime
4. Calendar ReservationDate
5. InputField Purpose
6. Text ReservationNumber
7. Text PersonName
8. Text AffiliationName
9. Text PhoneNumber
10. Button SaveButton
11. Button CloseButton

Methods

1. OnSave()
2. OnClose