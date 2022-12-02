# LR21

Кириченко Антон

ЭВТ-70

Игровой Движок: Unity.

Лабораторная работа № 21

Тема: разработка игры CARS

Цель: Разработать игру CARS

Ход работы

1.	Скрипт Car
```
public class Car : MonoBehaviour
{
    public bool FirstLaneBlueCar, FirstLaneOrangeCar;
    public bool BlueCar;
    public Vector2 Xpos;
    // Update is called once per frame
    void Update()
    {
        if (Input.GetKeyDown("left"))
        {
            LeftButtonPressed();
        }
        if (Input.GetKeyDown("right"))
        {
            RightButtonPressed();
        }
        if (BlueCar)
        {
            if (FirstLaneBlueCar)
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(-Xpos.y, transform.position.y, 0), .1f);
            }
            else
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(-Xpos.x, transform.position.y, 0), .1f);
            }
        }
        else
        {
            if (FirstLaneOrangeCar)
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(Xpos.y, transform.position.y, 0), .1f);
            }
            else
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(Xpos.x, transform.position.y, 0), .1f);
            }
        }
    }
    public void LeftButtonPressed()
    {
        if (FirstLaneBlueCar) { FirstLaneBlueCar = false; } else { FirstLaneBlueCar = true; }
    }
    public void RightButtonPressed()
    {
        if (FirstLaneOrangeCar) { FirstLaneOrangeCar = false; } else { FirstLaneOrangeCar = true; }
    }
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Circle")
        {
            //give Points
            FindObjectOfType<GameController>().AddScore();
            Destroy(collision.gameObject);
        }
        if (collision.tag == "Cube")
        {
            //gameOver
            FindObjectOfType<GameController>().GameOver();
            Debug.Log("playerGO");
        }
    }
}
2.	Скрипт CircleSquaerGO.
public class CircleSquareGO : MonoBehaviour
{
    int speed;
    Rigidbody2D rgbd;
    // Start is called before the first frame update
    void Start()
    {
        speed = 10;
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.velocity = new Vector2(0, -speed);
    }
}
```
3.	 Создали пустой объект Bg и добавил компонент Sprite Renderer, с спрайт BackGround. и настройка Main Camera и остальных объектов

_____![image](https://user-images.githubusercontent.com/119228138/205000033-d4f92709-c834-4a02-8aa0-5a61b3aba253.png) ![image](https://user-images.githubusercontent.com/119228138/205000053-b05d931b-8609-4814-b946-15782c212124.png)

_________________Рис. 21.1 Inspector объектов Sprite

4.	В пустом объекте Circle Blue создал объект через 2D Object – Sprites, куда добавил спрайт Blue_circle и т.д. их настройка.   Таким же образом сделал игровые объекты другого типа. 
 
 ![image](https://user-images.githubusercontent.com/119228138/205000160-1611781a-9677-4527-b2a0-04be315f692d.png)
 
_________________Рис. 21.2 Рис. 21.2 Inspector объекта Squaer Orange

![image](https://user-images.githubusercontent.com/119228138/205000198-68ee9a04-e1e6-4ae1-96a2-1921a7131352.png)![image](https://user-images.githubusercontent.com/119228138/205000221-c9939ae7-7da9-44ac-8140-fe15ef553762.png)


_________________Рис. 21.3 Префабы, Inspector объекта GameController и объекта EdgeCollider

5.	Скрипт GameController
```
public class GameController : MonoBehaviour
{   //Circle And Square GO
    public GameObject[] BlueGO;
    public GameObject[] OrangeGO;
    //To instantiate squate and circle
    public float startWait;
    public float spawnWait;
    //which GO to Instantiate
    GameObject Blue, Orange;
    //spawn point spawn randomly X pos
    float[] XPosition = new float[2] { 1.5f, 4.6f };
    bool GameOverBool;
    int Score;
    public Text ScoreText;
    public GameObject GameOverCanvas;
    // Start is called before the first frame update
    void Start()
    {
        GameOverBool = false;
        Time.timeScale = 1;
        GameOverCanvas.SetActive(false);
    }
    IEnumerator SpawnObjects()
    {
        yield return new WaitForSeconds(startWait);
        while (true)
        {
            for (int i = 0; i < 50; i++)
            {
                //choosing Xpos
                float OrangeXpos = XPosition[Random.Range(0, XPosition.Length)];
                //setting position
                Vector3 OrangePos = new Vector3(OrangeXpos, 10, 0);
                //choosing between square or circle
                Orange = OrangeGO[Random.Range(0, OrangeGO.Length)] as GameObject;
                //Instantiate now
                Instantiate(Orange, OrangePos, Quaternion.identity);
                yield return new WaitForSeconds(spawnWait);
                float BlueXpos = -XPosition[Random.Range(0, XPosition.Length)];
                //setting position
                Vector3 BluePos = new Vector3(BlueXpos, 10, 0);
                //choosing between square or circle
                Blue = BlueGO[Random.Range(0, BlueGO.Length)] as GameObject;
                //Instantiate now
                Instantiate(Blue, BluePos, Quaternion.identity);
                yield return new WaitForSeconds(spawnWait);
            }
           
        }
    }
    public void StartGame()
    {   
        StartCoroutine(SpawnObjects());
    }
    public void GameOver()
    {
        GameOverBool = true;
        Time.timeScale = 0;
        GameOverCanvas.SetActive(true);
    }
    public void AddScore()
    {
        Score += 1;
        ScoreText.text = Score.ToString();
    }
    public void Restart()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}
```

6.	Скрипт EdgeCollider

```
public class EdgeCollider : MonoBehaviour
{
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Circle")
        {
            //gameOver
            FindObjectOfType<GameController>().GameOver();
            Debug.Log("GameOver");
        }
        else if (collision.tag == "Cube")
        {
            //destroy go
            Destroy(collision.gameObject);
        }
    }
}
```

7.	Создан объект StartGameCanvas:  и Canvas с настройками Scaler – Scale with screen size и выстваил разрешение 480х800. Потом добавили  объект Button, объект GameController и StartGameCanvas. Создали объект panel, у которого выставили черный цвет.

![image](https://user-images.githubusercontent.com/119228138/205000423-dddaa681-6c6a-4a7b-861a-ecc5d6fcbae4.png)![image](https://user-images.githubusercontent.com/119228138/205000444-8c66bd6e-eb32-4b3a-8f33-915428822954.png)

_________________Рис. 21.4 Inspector объектов StartGameCanvas и Button

8.	Создали объект GameCanvas:  Скопировал его и изменил под правую сторону. 

![image](https://user-images.githubusercontent.com/119228138/205000521-5936bd3a-1ff1-47f5-a7f2-31c91eb4f298.png)![image](https://user-images.githubusercontent.com/119228138/205000533-e517c90f-b125-4e6b-8bd9-465a63c6a6fc.png)


_________________Рис. 21.4 Inspector объектов LeftHalfButton и RightHalfButton

9.	Создали объект ScoreCanvas. с дочерним объектом Text:

![image](https://user-images.githubusercontent.com/119228138/205000565-526184fd-6895-4b6f-8945-71f46113a9db.png)

_________________Рис. 21.4 Inspector объекта Text

Вывод:В ходе проделанной работы были приобретены навыки в разработке игры 2 CARS.

[LR21 (1).zip](https://github.com/Userfall3000/LR-21/files/10142218/LR21.1.zip)
