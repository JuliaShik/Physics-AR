# Дипломная работа: AR-приложение. Как это было
Как это бывает, с наступлением последнего учебного года пришла пора выбирать тему дипломной работы. Специальность, по которой я училась - информатика и вычислительная техника, а работала я в это время frontend-разработчиком и делала сайты. Казалось, выбор темы очевиден, это будет что-то из веба, но нет! Темой моего диплома стала разработка мобильного AR приложения, ибо очень уж мне хотелось попробовать сделать дополненную реальность 👻

И вот что получилось:


## Описание приложения
**_Тема:_** Мобильное приложение AR для изучения школьного курса физики

**_Технологии:_** [Unity](https://unity.com/ru), [AR Foundation](https://unity.com/ru/unity/features/arfoundation), [ARKit](https://developer.apple.com/documentation/arkit), [ARCore](https://developers.google.com/ar), C#

![Alt Text](20200623_110406_001.gif)

Пользователь выбирает раздел физики, изучает теорию и переходит к 3d-сцене. Далее нужно навести камеру телефона на маркерное изображение или горизонтальную плоскость. Алгоритмы компьютерного зрения распознают выбранную цель. Пользователь касается экрана и ВАУ! МАГИЯ ✨✨✨ К точке касания привязываются 3d-объекты, с которыми тоже можно взаимодействовать и управлять ими.

## Разработка

### Начало

Первым делом необходимо создать проект в Unity Hub и добавить программные модули, отвечающие за сборку проекта под различные платформы на устройствах, в моем случае это Android SDK & NDK Tools, Open JDK. Далее импортируем в проект ARFoundation и необходимые для ее работы ARKit XR Plugin и ARCore XR Plugin. Готово!

### Добавление AR

После разработки 3d-моделей, написания кода анимации и пользовательского интерфейса пришла пора добавить в проект AR.
Базовая иерархия AR-сцен состоит из ARSession и ARSessionOrigin/AR Camera. Цель ARSessionOrigin состоит в том, чтобы преобразовать отслеживаемые объекты (такие как плоские поверхности и характерные точки) в их конечное положение, ориентацию и масштаб в сцене Unity. Основная логика прописана в классе ARTapToPlaceObj. После инициализации ARRaycastManager т.е. когда цель найдена, с помощью метода TryGetTouchPosition, каждый фрейм производится проверка на событие касания пользователем экрана. Как только это произойдет, в вектор touchPosition будут записаны координаты касания, которые потом будут преобразованы в набор точек hitPose, содержащий значения позиции и ротации контента.

```c#
public class ARTapToPlaceObj : MonoBehaviour
{
    public GameObject gameObjectToInstantiate;
    private GameObject spawnedObject;
    private ARRaycastManager _arRaycastManager;
    private Vector2 touchPosition;
    static List<ARRaycastHit> hits = new List<ARRaycastHit>();

    private void Awake()
    {
        _arRaycastManager = GetComponent<ARRaycastManager>();
    }

    bool TryGetTouchPosition(out Vector2 touchPosition)
    {
        if(Input.touchCount > 0)
        {
            touchPosition = Input.GetTouch(0).position;
            return true;
        }

        touchPosition = default;
        return false;
    }

    void Update()
    {
        if(!TryGetTouchPosition(out Vector2 touchPosition))
            return;

        if(_arRaycastManager.Raycast(touchPosition, hits, TrackableType.PlaneWithinPolygon))
        {
            var hitPose = hits[0].pose;

            if(spawnedObject == null)
            {
                spawnedObject = Instantiate(gameObjectToInstantiate, hitPose.position, hitPose.rotation);
            }
        }
    }
}

```
## Заключение

Разрабатывать AR-приложение было очень интересно. Пока что в приложении только четыре сцены с дополненной реальностью. В планах добавить в проект больше контента и реализовать поддержку на платформу IOS.

Спасибо за внимание!

