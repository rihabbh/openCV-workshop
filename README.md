# openCV-workshop

```
import cv2
```

### Lecture d'une image

```
img = cv2.imread(path)

cv2.imshow("Titre",img)
```

Pour ajouter un délai, en millisecond


```
cv2.waitKey(0)
```

### Lecture d'une video

```
cap = cv2.VideoCapture(path)

#Une video est une succession d'image
while True:
    success,img = cap.read()
    cv2.imshow("Titre de la video",img)
    # Arrêt de la video
    if cv2.waitKey(1) & OxFF == ord('q'):
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
    if cv2.waitKey(1) & OxFF == ord('q'):
        break
```

Modifier la luminosité 

```
cap.set(10,100)
```

## Fonction de base 


