# color-deduction-
import cv2
import pandas as pd
import numpy as np

#These lines are reading csv file having color combinations with names
index=["color","color_name","hex","R","G","B"]
csv = pd.read_csv('colors.csv', names=index, header=None)
r = g = b = xpos = ypos = 0


#function to calculate minimum distance from all colors and get the most matching color
def getColorName(R,G,B):
    minimum = 10000
    for i in range(len(csv)):
        d = abs(R- int(csv.loc[i,"R"])) + abs(G- int(csv.loc[i,"G"]))+ abs(B- int(csv.loc[i,"B"]))
        if(d<=minimum):
            minimum = d
            cname = csv.loc[i,"color_name"]
    return cname
#function to get x,y coordinates of mouse double click
def mouseRGB(event,x,y,flags,param):
    if event == cv2.EVENT_LBUTTONDOWN: #checks mouse left button down condition
        global b,g,r,xpos,ypos, clicked
        clicked = True
        img = frame
        xpos = x
        ypos = y
        b, g, r = img[y, x]
        b = int(b)
        g = int(g)
        r = int(r)

def img_show(event,x,y,flags,param):
    if event == cv2.EVENT_LBUTTONDOWN: #checks mouse left button down condition
        global b, g, r, xpos, ypos, clicked
        clicked = False
        cv2.namedWindow('image')
        cv2.setMouseCallback('image', mouseRGB)
        img = frame
        while (1):
            cv2.imshow("image", img)

            if (clicked):

                # cv2.rectangle(image, startpoint, endpoint, color, thickness)-1 fills entire rectangle
                cv2.rectangle(img, (20, 20), (750, 60), (b, g, r), -1)

                # Creating text string to display( Color name and RGB values )
                text = getColorName(r, g, b) + ' R=' + str(r) + ' G=' + str(g) + ' B=' + str(b)

                # cv2.putText(img,text,start,font(0-7),fontScale,color,thickness,lineType )
                cv2.putText(img, text, (50, 50), 2, 0.8, (255, 255, 255), 2, cv2.LINE_AA)

                # For very light colours we will display text in black colour
                if (r + g + b >= 600):
                    cv2.putText(img, text, (50, 50), 2, 0.8, (0, 0, 0), 2, cv2.LINE_AA)

                clicked = False

            # Break the loop when user hits 'C' key
            if cv2.waitKey(20) & 0xFF == ord('c'):
                break

        cv2.destroyWindow('image')




cv2.namedWindow('video')
cv2.setMouseCallback('video',img_show)



capture = cv2.VideoCapture(0)

while(True):

    ret, frame = capture.read()

    cv2.imshow('video', frame)

    if cv2.waitKey(20) & 0xFF == ord('x'):
        break

capture.release()
cv2.destroyAllWindows()
