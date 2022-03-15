<!-- ----!
Presentation
----! -->

# GPDMA handson adding features

## Current state

* ADC convert samples
* ADC generate request for GPDMA
* GPDMA transfer data to memory

## Next Step

* Now we do the same but with **LLI list**.
* Which allow us later add more lists in later steps

# Return back to CubeMX

In MX the ADC configuration will be the same 

# Change GPDMA mode

1. Change GPDMA mode from `Standard Request Mode` to `Linked-List Mode`

![set list mode](./img/22_03_08_103.png)

# Configure CH15

1. Got to CH15 Configuration

![set list mode](./img/22_03_08_105.png)

2. Set **Execution Mode of Linked List** to `Circular`

![set circular mode](./img/22_03_08_107.png)

# Linked List configuration

1. Go to `LINKEDLIST` periphery

![LINKEDLIST periphery](./img/22_03_08_109.gif)

2. Add List by click on `Add List` button

![Add list](./img/22_03_08_111.png)

# Configue List/Queue

1. Click on Queue to be able configure it. Default name is `YourQueueName`

![select queue](./img/22_03_08_113.png)

2. Set **Linear or cicrular LinkedList setting** to `Circular`

![select circular mode](./img/22_03_08_115.png)

3. Set first node in loop in our case put `YourNodeName`

```c
YourNodeName
```

![select first node name](./img/22_03_08_117.png)

# Node configuration

1. Select Node

![select first node name](./img/22_03_08_119.png)


# Set node parameters same as in previous configuration

1. In **Request configuration ** set **Request as a patameter** to `GPDMA_REQUEST_ADC1`
   
![request](./img/22_03_08_121.gif)

2. In **Destination Data Setting** set **Destination Address Increment After transfer** to `Enabled`

3. In **Destination Data Setting** set **Data Width** to `Half Word`

![destination configuration](./img/22_03_08_123.gif)

4. In **Source Data Setting** set **Data Width** to `Half Word`

![source configuration](./img/22_03_08_129.gif)

5. In **Runtime configuration** set **Source Address** to `ADC1->DR`

```c
&(ADC1->DR)
```

6. In **Runtime configuration** set **Destination Address** to `data`

```c
data
```

7. In **Runtime configuration** set **Data Size** to `SIZE`

```c
SIZE
```

![Runtime configuration](./img/22_03_08_127.png)

# Generate code

Now generate code and switch to CubeIDE


# Add linked_list.h include

First add **include** of `linked_list.h`

By adding 

```c
#include "linked_list.h"
```

To `USER CODE BEGIN Includes` section

```c-nc
/* USER CODE BEGIN Includes */
#include "linked_list.h"
/* USER CODE END Includes */
```

# Add Queue handle to main

Add `YourQueueName` extern variable to our `main.c`

By adding 

```c
extern DMA_QListTypeDef YourQueueName;
```

like 

```c-nc
/* USER CODE BEGIN PV */
#define SIZE 64
uint16_t data[SIZE];

extern DMA_QListTypeDef YourQueueName;
/* USER CODE END PV */
```

# Add size and data also to Queue config

Add 

```c
#define SIZE 64
extern uint16_t data[];
```

to `linked_list.c` section `/* USER CODE BEGIN PM */` like

```c-nc
/* USER CODE BEGIN PM */
#define SIZE 64
extern uint16_t data[];
/* USER CODE END PM */
```

# Call queue config

Use `MX_YourQueueName_Config` to initialize our nodes and queue.

Add

```c
  MX_YourQueueName_Config();
```

to `/* USER CODE BEGIN 2 */` section like


```c-nc
  /* USER CODE BEGIN 2 */
  MX_YourQueueName_Config();
  /* USER CODE END 2 */
```

# Connect queue and GPDMA channel

To connect Queue and GPDMA we will use `HAL_DMAEx_List_LinkQ`

Add

```c
HAL_DMAEx_List_LinkQ(&handle_GPDMA1_Channel15, &YourQueueName);
```

to section `/* USER CODE BEGIN 2 */` like

```c-nc
  /* USER CODE BEGIN 2 */
  MX_YourQueueName_Config();
  
  HAL_DMAEx_List_LinkQ(&handle_GPDMA1_Channel15, &YourQueueName);
  /* USER CODE END 2 */
```

First parameter `handle_GPDMA1_Channel15` is GPDMA handle
Second parameter `YourQueueName` is our Queue

# Start GDMA and ADC

Start DMA by using `HAL_DMAEx_List_Start`
ADC is started by using `HAL_ADC_Start`

Add 

```c
  HAL_DMAEx_List_Start(&handle_GPDMA1_Channel15);
  HAL_ADC_Start(&hadc1);
```

To section `/* USER CODE BEGIN 2 */` like 

```c-nc
  /* USER CODE BEGIN 2 */
  MX_YourQueueName_Config();

  HAL_DMAEx_List_LinkQ(&handle_GPDMA1_Channel15, &YourQueueName);

  HAL_DMAEx_List_Start(&handle_GPDMA1_Channel15);
  HAL_ADC_Start(&hadc1);
  /* USER CODE END 2 */
```

## Compile code and run debug

# What we created

We have similar application like in handson 1 but now based directly on linked list, where we can more elements