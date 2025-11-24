# Micrófono y Cámara

- **Alumno:** Adrián García Rodríguez
- **Correo:** alu0101557977@ull.edu.es

## Ejercicio 1

Para la realización del primer ejercicio, se han dispuesto en la escena dos soldados sobre un plano. El primer soldado cuenta con un `Collider` y un `Rigidbody`, mientras que el segundo tan solo cuenta con un `Collider`.

Para el primer soldado, se ha desarrollado un script que permite el control de su movimiento, considerando su condición de objeto físico.

```c#
using UnityEngine;

public class Mover : MonoBehaviour
{
  private Rigidbody rb;
  private Vector3 direction;

  public float speed = 6f;

  void Start()
  {
    rb = GetComponent<Rigidbody>();
  }

  void Update()
  {
    float leftRight = Input.GetAxisRaw("Horizontal");
    float forwardBackward = Input.GetAxisRaw("Vertical");

    direction = new Vector3(leftRight, 0f, forwardBackward).normalized;
  }

  void FixedUpdate()
  {
    rb.MovePosition(rb.position + direction * speed * Time.fixedDeltaTime);
  }  
}
```

Asimismo, se le ha añadido un componente `AudioSource` y, junto a él, un script que reproduce el sonido cuando se detecta una colisión con el segundo soldado.

```c#
using System;
using UnityEngine;

public class CombatSound : MonoBehaviour
{
  private AudioSource audioPlay;

  void Start()
  {
    audioPlay = GetComponent<AudioSource>();
  }

  void OnCollisionEnter(Collision collision)
  {
    if (collision.gameObject.tag == "Enemy")
      audioPlay.Play();
  }
}
```

Ejemplo visual:

[![combate-con-sonido](https://img.youtube.com/vi/lkCqwPnJ7sk/maxresdefault.jpg)](https://youtu.be/lkCqwPnJ7sk)

▶️ [Ver en YouTube](https://youtu.be/lkCqwPnJ7sk)

## Ejercicio 2

En este ejercicio, se ha importado un asset de televisor antiguo desde la `Unity Asset Store`, y otro de unos altavoces de PC.

Seguidamente, se han añadido a los altavoces un componente `AudioSource` para poder guardar el `AudioClip` grabado por el micrófono y así poder reproducirlo posteriormente.

Por último, se ha desarrollado un script que, al mantener presionada la tecla `Space`, se graba un audio de 10 segundos. Para reproducir el audio generado, se deberá de mantener presionada la tecla `R`.

```c#
using UnityEngine;

public class RecordAudio : MonoBehaviour
{
  private AudioSource source;

  void Start()
  {
    source = GetComponent<AudioSource>();
  }

  void Update()
  {
    if (Input.GetKeyDown(KeyCode.Space))
      source.clip = Microphone.Start(null, false, 10, 44100);
    if (Input.GetKeyUp(KeyCode.Space))
      Microphone.End(null);
    if (Input.GetKeyDown(KeyCode.R))
      source.Play();
    if (Input.GetKeyUp(KeyCode.R))
      source.Stop();
  } 
}
```

Ejemplo visual:

[![grabación-de-sonido](https://img.youtube.com/vi/ZroYSzSIwz4/maxresdefault.jpg)](https://youtu.be/ZroYSzSIwz4)

▶️ [Ver en YouTube](https://youtu.be/ZroYSzSIwz4)

## Ejercicio 3

Para el último ejercicio, se ha colocado un plano con las medidas justas de la pantalla en frente del televisor. Inicialmente, el plano tiene un material transparente, para que no se note su presencia. Para la captura y reproducción de la cámara, se ha diseñado un script con diferentes acciones. A continuación se describen las posible acciones:

- **Pulsar la tecla S:** crea un nuevo material y lo asigna al plano, cambia el atributo `mainTexture` del mismo, pasando a ser el `WebCamTexture` e inicia la captura con el método `Play()`.
- **Pulsar la tecla P:** detiene la captura de la cámara con el método `Stop()`, cambiando además el material del plano al original.
- **Pulsar la tecla X:** crea una variable de tipo `Texture2D` y almacena en ella los píxeles de la `WebCamTexture` en ese momento. Finalmente, guarda el contenido de la textura en la ruta especificada en formato `.png`.

```c#
using UnityEngine;
using System.IO;

public class RecordScreen : MonoBehaviour
{
  private Renderer rend;
  private Material originalMaterial;
  private WebCamTexture webcamTexture;
  private string savePath;
  int captureCounter = 1;

  void Start()
  {
    rend = GetComponent<Renderer>();
    originalMaterial = rend.material;
    WebCamDevice[] devices = WebCamTexture.devices;
    Debug.Log("Camera: " + devices[0].name);
    webcamTexture = new WebCamTexture(devices[0].name);
    savePath = Application.dataPath + "/SnapShots/";
    if (!Directory.Exists(savePath))
      Directory.CreateDirectory(savePath);
  }

  void Update()
  {
    if (Input.GetKey(KeyCode.S))
    {
      rend.material = new Material(Shader.Find("Universal Render Pipeline/Lit"));
      rend.material.mainTexture = webcamTexture;
      webcamTexture.Play();
    }
    if (Input.GetKey(KeyCode.P)) {
      rend.material = originalMaterial;
      webcamTexture.Stop();
    }
    if (Input.GetKeyDown(KeyCode.X))
      CaptureImage();
  }

  void CaptureImage()
  {
    Texture2D snapshot = new Texture2D(webcamTexture.width,
                                       webcamTexture.height);
    snapshot.SetPixels(webcamTexture.GetPixels());
    snapshot.Apply();
    string filename = savePath + "snapshot_" + captureCounter + ".png";
    File.WriteAllBytes(filename, snapshot.EncodeToPNG());
    ++captureCounter;
  }
}
```

Ejemplo visual:

[![grabación-de-sonido](https://img.youtube.com/vi/YtHcsaLVAMA/maxresdefault.jpg)](https://youtu.be/YtHcsaLVAMA)

▶️ [Ver en YouTube](https://youtu.be/YtHcsaLVAMA)