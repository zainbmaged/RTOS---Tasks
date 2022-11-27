/*
 * FreeRTOS Kernel V10.2.0
 * Copyright (C) 2019 Amazon.com, Inc. or its affiliates.  All Rights Reserved.
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy of
 * this software and associated documentation files (the "Software"), to deal in
 * the Software without restriction, including without limitation the rights to
 * use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
 * the Software, and to permit persons to whom the Software is furnished to do so,
 * subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
 * FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
 * COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
 * IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
 * CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
 *
 * http://www.FreeRTOS.org
 * http://aws.amazon.com/freertos
 *
 * 1 tab == 4 spaces!
 */

/* 
	NOTE : Tasks run in system mode and the scheduler runs in Supervisor mode.
	The processor MUST be in supervisor mode when vTaskStartScheduler is 
	called.  The demo applications included in the FreeRTOS.org download switch
	to supervisor mode prior to main being called.  If you are not using one of
	these demo application projects then ensure Supervisor mode is used.
*/


/*
 * Creates all the demo application tasks, then starts the scheduler.  The WEB
 * documentation provides more details of the demo application tasks.
 * 
 * Main.c also creates a task called "Check".  This only executes every three 
 * seconds but has the highest priority so is guaranteed to get processor time.  
 * Its main function is to check that all the other tasks are still operational.
 * Each task (other than the "flash" tasks) maintains a unique count that is 
 * incremented each time the task successfully completes its function.  Should 
 * any error occur within such a task the count is permanently halted.  The 
 * check task inspects the count of each task to ensure it has changed since
 * the last time the check task executed.  If all the count variables have 
 * changed all the tasks are still executing error free, and the check task
 * toggles the onboard LED.  Should any task contain an error at any time 
 * the LED toggle rate will change from 3 seconds to 500ms.
 *
 */

/* Standard includes. */
#include <stdlib.h>
#include <stdio.h>

/* Scheduler includes. */
#include "FreeRTOS.h"
#include "task.h"
#include "lpc21xx.h"

/* Peripheral includes. */
#include "serial.h"
#include "GPIO.h"
#include "semphr.h"
#include "queue.h"

/*-----------------------------------------------------------*/

/* Constants to setup I/O and processor. */
#define mainBUS_CLK_FULL	( ( unsigned char ) 0x01 )

/* Constants for the ComTest demo application tasks. */
#define mainCOM_TEST_BAUD_RATE	( ( unsigned long ) 115200 )

TaskHandle_t String_100_Handler = NULL;
TaskHandle_t uart_Handler = NULL;
//TaskHandle_t rising_edge_Handler = NULL;
TaskHandle_t Button_1_Handler = NULL;


/*
 * Configure the processor for use with the Keil demo board.  This is very
 * minimal as most of the setup is managed by the settings in the project
 * file.
 */
static void prvSetupHardware( void );
/*-----------------------------------------------------------*/

TickType_t start_timer;
pinState_t Button_1_State_new;
pinState_t Button_1_State_old;
pinState_t Button_2_State_new;
pinState_t Button_2_State_old;
//int edge_flag;//set to 1 when rising edge is detected set to 2 when falling edge is detected
/* Define a variable of type struct AMMessage.  The examples below demonstrate
how to pass the whole variable through the queue, and as the structure is
moderately large, also how to pass a reference to the variable through a queue. */
struct AMessage
{
 signed char ucMessageID;
 signed  char ucData[ 20 ];
} xMessage;

/* Queue used to send and receive complete struct AMessage structures. */
QueueHandle_t xStructQueue = NULL;

/* Queue used to send and receive pointers to struct AMessage structures. */
QueueHandle_t xPointerQueue = NULL;

//Task3 to detect button_1 rising and falling edge
void Button_1( void * pvParameters )
{
	pinState_t Button_1_State_old= PIN_IS_LOW;//let botton intially low
    
    for( ;; )
    {
			
			
			 // read Button and save its state in Button_State global variable
			Button_1_State_new = GPIO_read(PORT_0,PIN0);
			//if button is pushed but not released 
			 if(Button_1_State_new == PIN_IS_HIGH && Button_1_State_old==PIN_IS_LOW){
				 //rising edge detected
				struct AMessage xMessage ={'2',"rising_edge1"};
			  struct AMessage *pxPointerToxMessage;
				 Button_1_State_old =PIN_IS_HIGH;//set the old value to high
				 /* Send the entire structure to the queue created to hold 10 structures. */
   xQueueSend( /* The handle of the queue. */
               xStructQueue,
               /* The address of the xMessage variable.  sizeof( struct AMessage )
               bytes are copied from here into the queue. */
               ( void * ) &xMessage,
               /* Block time of 0 says don't block if the queue is already full.
               Check the value returned by xQueueSend() to know if the message
               was sent to the queue successfully. */
               ( TickType_t ) 0 );
				}
			 Button_1_State_new = GPIO_read(PORT_0,PIN0);
				
			//if button is released
			if(Button_1_State_new == PIN_IS_LOW &&Button_1_State_old==PIN_IS_HIGH){
			 //falling edge detected
				struct AMessage xMessage ={'3',"falling_edge1"};
			  struct AMessage *pxPointerToxMessage;
				Button_1_State_old =PIN_IS_LOW;//set the old value to low
				 /* Send the entire structure to the queue created to hold 10 structures. */
   xQueueSend( /* The handle of the queue. */
               xStructQueue,
               /* The address of the xMessage variable.  sizeof( struct AMessage )
               bytes are copied from here into the queue. */
               ( void * ) &xMessage,
               /* Block time of 0 says don't block if the queue is already full.
               Check the value returned by xQueueSend() to know if the message
               was sent to the queue successfully. */
               ( TickType_t ) 0 );
							 
							
			}
			
    }
}
//Task3 to detect button_1 rising and falling edge
void Button_2( void * pvParameters )
{
	pinState_t Button_2_State_old= PIN_IS_LOW;//let botton intially low
    
    for( ;; )
    {
			
			
			 // read Button and save its state in Button_State global variable
			Button_2_State_new = GPIO_read(PORT_0,PIN1);
			//if button is pushed but not released 
			 if(Button_2_State_new == PIN_IS_HIGH && Button_2_State_old==PIN_IS_LOW){
				 //rising edge detected
				struct AMessage xMessage ={'2',"rising_edge2"};
			  struct AMessage *pxPointerToxMessage;
				 Button_2_State_old =PIN_IS_HIGH;//set the old value to high
				 /* Send the entire structure to the queue created to hold 10 structures. */
   xQueueSend( /* The handle of the queue. */
               xStructQueue,
               /* The address of the xMessage variable.  sizeof( struct AMessage )
               bytes are copied from here into the queue. */
               ( void * ) &xMessage,
               /* Block time of 0 says don't block if the queue is already full.
               Check the value returned by xQueueSend() to know if the message
               was sent to the queue successfully. */
               ( TickType_t ) 0 );
				}
			 Button_2_State_new = GPIO_read(PORT_0,PIN1);
				
			//if button is released
			if(Button_2_State_new == PIN_IS_LOW &&Button_2_State_old==PIN_IS_HIGH){
			 //falling edge detected
				struct AMessage xMessage ={'3',"falling_edge2"};
			  struct AMessage *pxPointerToxMessage;
				Button_2_State_old =PIN_IS_LOW;//set the old value to low
				 /* Send the entire structure to the queue created to hold 10 structures. */
   xQueueSend( /* The handle of the queue. */
               xStructQueue,
               /* The address of the xMessage variable.  sizeof( struct AMessage )
               bytes are copied from here into the queue. */
               ( void * ) &xMessage,
               /* Block time of 0 says don't block if the queue is already full.
               Check the value returned by xQueueSend() to know if the message
               was sent to the queue successfully. */
               ( TickType_t ) 0 );
							 
							
			}
			
    }
}
/* Task1  send string to uart */
void uart( void * pvParameters )
{	
 
     for( ;; )
     {
			    struct AMessage xRxedStructure, *pxRxedPointer;
				
   if( xStructQueue != NULL )
   {
      /* Receive a message from the created queue to hold complex struct AMessage
      structure.  Block for 10 ticks if a message is not immediately available.
      The value is read into a struct AMessage variable, so after calling
      xQueueReceive() xRxedStructure will hold a copy of xMessage. */
      if( xQueueReceive( xStructQueue,
                         &( xRxedStructure ),
                         ( TickType_t ) 10 ) == pdPASS )
      {
         /* xRxedStructure now contains a copy of xMessage. */
				vSerialPutString(xRxedStructure.ucData,sizeof(xRxedStructure.ucData));
      }
   }



					
					
            
           
        }
       
			
 }
/* Task2  send string2 to uart 10 times each 100ms */
void String_100( void * pvParameters )
{	
  

     for( ;; )
     {
			struct AMessage xMessage ={'1',"string100"};
			struct AMessage *pxPointerToxMessage;
					
			  vTaskDelay(100);
					
   /* Send the entire structure to the queue created to hold 10 structures. */
   xQueueSend( /* The handle of the queue. */
               xStructQueue,
               /* The address of the xMessage variable.  sizeof( struct AMessage )
               bytes are copied from here into the queue. */
               ( void * ) &xMessage,
               /* Block time of 0 says don't block if the queue is already full.
               Check the value returned by xQueueSend() to know if the message
               was sent to the queue successfully. */
               ( TickType_t ) 0 );
			
            vTaskDelay(100);
           
        }
			
			 
 }


    
				
		
 



/*
 * Application entry point:
 * Starts all the other tasks, then starts the scheduler. 
 */
int main( void )
{
	/* Setup the hardware for use with the Keil demo board. */
	prvSetupHardware();
  
	
    xMessage.ucMessageID = 0xab;
  

   /* Create the queue used to send complete struct AMessage structures.  This can
   also be created after the schedule starts, but care must be task to ensure
   nothing uses the queue until after it has been created. */
   xStructQueue = xQueueCreate(
                         /* The number of items the queue can hold. */
                         10,
                         /* Size of each item is big enough to hold the
                         whole structure. */
                         sizeof( xMessage ) );

   /* Create the queue used to send pointers to struct AMessage structures. */
   xPointerQueue = xQueueCreate(
                         /* The number of items the queue can hold. */
                         10,
                         /* Size of each item is big enough to hold only a
                         pointer. */
                         sizeof( &xMessage ) );

   if( ( xStructQueue == NULL ) || ( xPointerQueue == NULL ) )
   {
      /* One or more queues were not created successfully as there was not enough
      heap memory available.  Handle the error here.  Queues can also be created
      statically. */
   }
	

			
	// save button state and set edge detection flag
				xTaskCreate(
                    Button_1,       /* Function that implements the task. */
                    "Button_1",          /* Text name for the task. */
                    100,      /* Stack size in words, not bytes. */
                    ( void * ) 0,    /* Parameter passed into the task. */
                    2,/* Priority at which the task is created. */
                    &Button_1_Handler );      /* Used to pass out the created task's handle. */		
										 //Toggle LED PIN2 each 0.5 second
										
  // save button state and set edge detection flag
				xTaskCreate(
                    Button_2,       /* Function that implements the task. */
                    "Button_2",          /* Text name for the task. */
                    100,      /* Stack size in words, not bytes. */
                    ( void * ) 0,    /* Parameter passed into the task. */
                    2,/* Priority at which the task is created. */
                    &Button_1_Handler );      /* Used to pass out the created task's handle. */		
										 //Toggle LED PIN2 each 0.5 second
	// send string  to uart every 100ms 
				xTaskCreate(
                    String_100,       /* Function that implements the task. */
                    "String_100",          /* Text name for the task. */
                    100,      /* Stack size in words, not bytes. */
                    ( void * ) 0,    /* Parameter passed into the task. */
                    2,/* Priority at which the task is created. */
                    &String_100_Handler );      /* Used to pass out the created task's handle. */		
										 //Toggle LED PIN2 each 0.5 second
										
										
				// send string to uart every 100ms 
				xTaskCreate(
                    uart,       /* Function that implements the task. */
                    "uart",          /* Text name for the task. */
                    100,      /* Stack size in words, not bytes. */
                    ( void * ) 0,    /* Parameter passed into the task. */
                    2,/* Priority at which the task is created. */
                    &uart_Handler );      /* Used to pass out the created task's handle. */		
										 //Toggle LED PIN2 each 0.5 second
			
//  	// send string to uart every 100ms 
//				xTaskCreate(
//                    rising_edge,       /* Function that implements the task. */
//                    "rising_edge",          /* Text name for the task. */
//                    100,      /* Stack size in words, not bytes. */
//                    ( void * ) 0,    /* Parameter passed into the task. */
//                    2,/* Priority at which the task is created. */
//                    &rising_edge_Handler );      /* Used to pass out the created task's handle. */		
//										 //Toggle LED PIN2 each 0.5 second
//			

								
     
			  
					

	/* Now all the tasks have been started - start the scheduler.

	NOTE : Tasks run in system mode and the scheduler runs in Supervisor mode.
	The processor MUST be in supervisor mode when vTaskStartScheduler is 
	called.  The demo applications included in the FreeRTOS.org download switch
	to supervisor mode prior to main being called.  If you are not using one of
	these demo application projects then ensure Supervisor mode is used here. */
	vTaskStartScheduler();

	/* Should never reach here!  If you do then there was not enough heap
	available for the idle task to be created. */
	for( ;; );
}
/*-----------------------------------------------------------*/

static void prvSetupHardware( void )
{
	/* Perform the hardware setup required.  This is minimal as most of the
	setup is managed by the settings in the project file. */

	/* Configure UART */
	xSerialPortInitMinimal(mainCOM_TEST_BAUD_RATE);

	/* Configure GPIO */
	GPIO_init();

	/* Setup the peripheral bus to be the same as the PLL output. */
	VPBDIV = mainBUS_CLK_FULL;
}
/*-----------------------------------------------------------*/
