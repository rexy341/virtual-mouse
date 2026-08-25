```py

import cvzone.HandTrackingModule as cvzone
import cv2
import pyautogui
import math

cap = cv2.VideoCapture(0)
detector = cvzone.HandDetector(maxHands=2, detectionCon=0.2)

while True:
    success, img = cap.read()
    if not success:
        print("Failed to grab frame.")
        break
        
    img = cv2.flip(img, 1)
    hands, img = detector.findHands(img, draw=True) 
    
    grid_size = 50  
    h, w, _ = img.shape
    
    for x in range(0, w, grid_size):
        cv2.line(img, (x, 0), (x, h), (100, 100, 100), 1)  
        if x % 100 == 0:
            cv2.putText(img, str(x), (x + 2, 15), cv2.FONT_HERSHEY_PLAIN, 0.8, (0, 255, 255), 1)
    
    for y in range(0, h, grid_size):
        cv2.line(img, (0, y), (w, y), (100, 100, 100), 1)  
        if y % 100 == 0:
            cv2.putText(img, str(y), (5, y - 5), cv2.FONT_HERSHEY_PLAIN, 0.8, (0, 255, 255), 1)

    if hands:
        hand1 = hands[0]
        lmList1 = hand1["lmList"]
        hand_type = hand1["type"]
        fingers1 = detector.fingersUp(hand1)
        
        if hand1['type'] == "Right":
            fingers1[0] = 1 if fingers1[0] == 0 else 0
        
        print("Thumb: ", lmList1[4], "\n")
        #print("Index: ", lmList1[8], "\n")
        #print("Middle: ", lmList1[12], "\n")
        #print("Ring: ", lmList1[16], "\n")
        #print("Little: ", lmList1[20], "\n")
        print("Palm: ", lmList1[9],"\n")
        dist = math.hypot(lmList1[8][0] - lmList1[5][0], lmList1[8][1] - lmList1[5][1])
        
        #if fingers1[2] == 0 and fingers1[1] == 0 and fingers1[0] == 0:
        #    break
        
        if (lmList1[8][0]>500 and lmList1[8][1]<100):
            pyautogui.moveTo(1,0,1)
            pyautogui.hotkey('alt', 'f4')
            
    cv2.imshow("Tracker", img) 
    
    if cv2.waitKey(1) and 0xFF == ord('q') or (fingers1[2] == 0 and fingers1[1] == 0 and fingers1[0] == 0):
        break
        

# Clean up resources
cap.release()
cv2.waitKey(1)
cv2.destroyAllWindows()
print("Program exited cleanly.")
```