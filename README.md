# Introduction à OpenCV

OpenCV, abréviation de Open Source Computer Vision Library, est une librairie de logiciels open source de Computer Vision et d'apprentissage automatique. Développé à l'origine par Intel, il est désormais maintenu par une communauté de développeurs sous la Fondation OpenCV.

OpenCV est une immense librairie open source pour la vision par ordinateur, l'apprentissage automatique et le traitement d'images. OpenCV prend en charge une grande variété de langages de programmation comme Python, C++, Java, etc. Il peut traiter des images et des vidéos pour identifier des objets, des visages ou même l'écriture manuscrite d'un humain. Lorsqu'il est intégré à diverses librairies, comme Numpyune librairie optimisée pour les opérations numériques, le nombre d'armes augmenté dans votre Arsenal, c'est-à-dire que toutes les opérations que l'on peut effectuer dans Numpy peuvent être combinés avec OpenCV.

Cette librairie est distribuée sous licence BSD.

Ce workshop vous permettra de maitriser les fonctions de bases d'OpenCV

Pour commencer, sur python, importer la librairie CV2:


```
import cv2
```

### Lecture d'une image

```
path = 'chemin vers l'image'
img = cv2.imread(path)

cv2.imshow("Titre",img)
```

Pour ajouter un délai, en milliseconde


```
cv2.waitKey(0)
```

### Lecture d'une video

```

path = 'chemin vers la video'

cap = cv2.VideoCapture(path)
img_counter = 1

#Une video est une succession d'image
while True:
    img_counter = img_counter + 1
    check, img = cap.read()
    if check:
        cv2.imshow("Cockatoo", img)
        key = cv2.waitKey(1)
    else:
        break
```


### Utilisation de la webcam


```
cap = cv2.VideoCapture(0)

## Definir la taille
cap.set(3,640)
cap.set(4,480) #Height

#Une video est une succession d'image
while True:
    success,img = cap.read()
    cv2.imshow("Titre de la video",img)
    # Arrêt de la video
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
```

Modifier la luminosité 

```
cap.set(10,100)
```

## Fonction de base 


Black & white / greyscale :

```
imgGray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
```

Blur :

```
imgBlur = cv2.GaussianBlur(img, (7,7),0)
```

Canny :

```
imgCanny = cv2.Canny(img, 100,100) # 150,200
```

Dialation :

```
import numpy as np

kernel =  np.ones((5,5),np.uint8)

imgDialation = cv2.dilate(imgCanny,kernel, iterations=1) 
```

Erode :

```
imgEroded = cv2.erode(imgDialation,kernel, iterations=1) 
```

## Rogner et Redimensionner


Connaitre la taille actuelle :

```
print(img.shape)
```

Redimensionner :

```
imgResize = cv2.resize(img,(width,heght)) 
```

Rogner :

```
imgCropped = img[heightStart:heightEnd,widthStart:widthEnd]
```


## Dessiner des formes

Commencer par créer une image avec un fond noir
```
img = np.zeros((512,512,3), np.uint8)
```

Vous pouvez changer la couleur ainsi : 
```
img[:] = 255,0,0
```

Pour créer un carré / rectangle
```
img[200:300,100:300] = 255,0,0
```

Une Ligne : 
```
cv2.line(img, (0,0), (300,300), (0,255,0),3)
```

```
cv2.line(img, (0,0), (img.shape[1],img.shape[0]), (0,255,0),3)
```

Une autre manière de dessiner un carré
```
cv2.rectangle(img,(0,0), (250,350), (0,0,255),3) # or cv2.FILLED
```

Un cercle : 
```
cv2.circle(img,(400,50),30,(0,255,255),5) 
```

Du texte 
```
cv2.putText(img,"OpenCV", (300,100), cv2.FONT_HERSHEY_COMPLEX,1, (0,150,0),1) 
```

## Wasp Perspective


```
img = cv2.imread("cards.jpg")

width,height = 250,350

# Les 4 côtés de la carte actuelle
pts1 = np.float32([[111,219],[287,188],[154,482],[352,440]])
#Les 4 points où l'on souhaite la transposer
pts2 = np.float32([[0,0],[width,0],[0,height],[width,height]])
# Matrice de transformation
matrix = cv2.getPerspectiveTransform(pts1,pts2)
imgOutput = cv2.warpPerspective(img,matrix,(width,height))


cv2.imshow("Image",img)
cv2.imshow("Output",imgOutput)

cv2.waitKey(0)
```

## Détection de contour/forme


```
path = "shapes.png"
img = cv2.imread(path)

cv2.imshow('Original',img)
cv2.waitKey(0)
```

Realiser un pré traitement de l'image
```
imgContour = img.copy()

imgGray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
imgBlur = cv2.GaussianBlur(imgGray,(7,7),1)
imgCanny = cv2.Canny(imgBlur,50,50)

cv2.imshow("imgCanny", imgCanny)

cv2.waitKey(0)
``` 

Créer une fonction pour obtenir les contours

```
def getContours(img):
    contours,hierarchy = cv2.findContours(img,cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_NONE)
```

Pour chaque contour detecter, on réalise un tracé 

```
def getContours(img):
    contours,hierarchy = cv2.findContours(img,cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_NONE)
    for cnt in contours:
        area = cv2.contourArea(cnt)
        cv2.drawContours(imgContour, cnt, -1, (255, 0, 0), 3)

getContours(imgCanny)
cv2.imshow("Shape", img)
cv2.imshow("Contour", imgContour)
cv2.waitKey(0)

```
Maintenant que l'on est capable de détecter les contours, utilisons ces informations pour trouver de quel type de figure il s'agit:

```
for cnt in contours:
        area = cv2.contourArea(cnt)

        if area>500:
            cv2.drawContours(imgContour, cnt, -1, (255, 0, 0), 3)
            peri = cv2.arcLength(cnt,True)
            #print(peri)
            approx = cv2.approxPolyDP(cnt,0.02*peri,True)
            print(len(approx))
            objCor = len(approx)
            x, y, w, h = cv2.boundingRect(approx)

            if objCor ==3: objectType ="Tri"
            elif objCor == 4:
                aspRatio = w/float(h)
                if aspRatio >0.98 and aspRatio <1.03: objectType= "Square"
                else:objectType="Rectangle"
            elif objCor>4: objectType= "Circles"
            else:objectType="None"

            cv2.rectangle(imgContour,(x,y),(x+w,y+h),(0,255,0),2)
            cv2.putText(imgContour,objectType,
                        (x+(w//2)-10,y+(h//2)-10),cv2.FONT_HERSHEY_COMPLEX,0.7,
                        (0,0,0),2)

getContours(imgCanny)
cv2.imshow("Contour", imgContour)
cv2.waitKey(0)

```

## Face Detection

Maitenant essayons de détecter des visages. L'entrainement d'un modèle prend du temps donc nous allons utilisé un modèle existant.

Commençons par importer le modèle :

```

faceCascade= cv2.CascadeClassifier("haarcascade/haarcascade/haarcascade_frontalface_default.xml")
``` 

On transforme l'image en nuance de gris et on applique le modèle : 

```
img = cv2.imread('model.jpg')
img = cv2.resize(img,(576,384)) 
imgGray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)

faces = faceCascade.detectMultiScale(imgGray,1.1,4)

``` 

Une fois les visage détecter on dessine un rectangle autour pour les localiser plus facilement: 

```
for (x,y,w,h) in faces:
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)


cv2.imshow("Result", img)
cv2.waitKey(0)
``` 




