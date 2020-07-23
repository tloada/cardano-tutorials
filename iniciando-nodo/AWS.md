# Accediendo a Linux en AWS

Para poder crear un _**node**_ (nodo) desde el _**source code**_ (código fuente), ejecutarlo y conectarlo a la _**mainnet**_ (red principal), necesitás un sistema operativo Linux, al menos 4GB de memoria RAM y 24GB libres en el disco duro. La memoria RAM es más que nada necesaria para _crear_ el nodo; para _ejecutarlo_, 1GB será más que suficiente. El espacio en el disco duro es necesario si querés conectarte a y descargar la _**blockchain**_ (cadena de bloques) de Cardano en su _mainnet_. Si vos solo querés preparar una testnet (red de prueba) local o conectarte a una _testnet_ externa, vas a necesitar menos de 24GB.

Si no tenés acceso a una computadora que use Linux, podés usar **Amazon Web Services (AWS)** para crear una máquina virtual localizada en la nube (**cloud**) que contenga Linux como su sistema operativo. Para hacer eso, seguí los siguientes pasos:

1. Andá a [Amazon Web Services (AWS)](https://aws.amazon.com/) y creá una cuenta (gratis) si no tenés una.

2. Andá a _**Consola de Administración de AWS**_.

3. Andá a _**EC2 Dashboard**_. _(Salvo por esta imagen, las demás estarán en inglés porque EC2 Dashboard todavía no está disponible en español)_

   ![AWS Management Console.](images/management_console.png)

4. Si ya tenés una _**instance**_ (instancia) ejecutando, saltate al paso 9.

5. Primero vamos a asegurarnos de tener un disco duro con suficiente espacio (mínimo 24GB). Hacé click en _**Volumes**_.

   ![Volumes in the Management Console.](images/volumes.png)

6. En el menú de _**Actions**_, sleccioná _**Modify Volume**_.

   ![Volume Actions.](images/volume_actions.png)

7. En el cuadro de diálogo de _**Modify Volume**_, sleccioná 24 y hace click en _**Modify*_, luego confirm tu selección.

   ![Modify Volume.](images/modify_volume.png)

8.	Volvé al _**EC2 Dashboard**_.

   ![EC2 Dashboard.](images/dashboard.png) 

9.	Hacé click en _**Launch Instance**_.

  ![Launch Instance.](images/launch_instance.png)

10. Usando _**Amazon Machine Image (AMI)**_ como opción, seleccioná _**Amazon Linux 2 (HVM)**_, _**SSD Volume Type**_, 64-bit (x86).

  ![Amazon Machine Image.](images/AMI.png)

11. Para tu _**Instance Type**_, seleccioná _**t2.medium**_, luego hacé click en _**Review and Launch**_ y finalmente en _**Launch**_ en la siguiente pantalla.

  ![Instance Type.](images/Instance_Type.png)

12. Creá un _**key pair**_ (par de llaves) o usá un par existente.

  ![Create a key pair.](images/key_pair.png)

13. Dale click en _**Connect**_ para conectarte a tu instancia.

  ![Connect.](images/connect.png)

14. Podés usar el _**EC2 Instance Connect**_ como tu _**connection method**_ (método de conexión).

  ![Choose connection method.](images/connect2.png)

15. Tecleá ``echo hello`` (y dale _**Enter**_) para probar si sirve la conexión. Este comando debería de regresar "hello" en tu consola.

  ![Trying the console.](images/connect3.png)

¡Felicidades! Ahora tenés acceso a una computadora que usa Linux.
