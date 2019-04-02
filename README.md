# msp430
#include "IIC.h"
unsigned int count=0;
void timer_init(void);
void key_init(void);//按键初始化，设置io口状态
void LED_init(void);
void delay(int i );
int main( void )
{
  // Stop watchdog timer to prevent time out reset
  WDTCTL = WDTPW + WDTHOLD;
unsigned char jud=0;
unsigned int timer_count=0;
IIC_ioinit();
LCD_Init();//液晶屏初始化
key_init();
LED_init();
LCD_ShowChinese(0,2,21);//显示电
LCD_ShowChinese(16,2,22);//显示子
LCD_ShowChinese(32,2,23);//科
LCD_ShowChinese(48,2,24);//技
LCD_ShowChinese(64,2,19);//大
LCD_ShowChinese(80,2,20);//学
delay(10000);//延迟
timer_init();
_EINT(); 
LCD_CLS();//清屏
LCD_ShowChinese(0,0,15);
LCD_ShowChinese(16,0,14);
while(1)
{
if(P2IN==0xfD)//P2.1按键判断
{
  delay(8);
  if(P2IN==0xfD)
  {
  count=0;
  }
}

if((P1IN&0x02)==0)//P1.1按键判断
{
  delay(10);
  if((P1IN&0x02)==0)
  {
      TA0CTL^= 0x0010;
  }
  
}
LCD_ShowNum(40,0,count,3,16);
LCD_ShowNum(40,2,P1IN&0x02,3,16);
}
}
void timer_init(void)
{
   TA0CTL|=TASSEL__ACLK;
  TA0CTL|=ID__1;
  TA0CTL|=MC__UP;
  //TA0CTL|=TAIE;
  TA0CCR0=32000;
  TA0CCTL0|=CCIE;
}
void key_init(void)
{
  P2DIR&=0xfD;//P2.1
  P2REN|=0X02;
  P2OUT|=0X02;
  
  P1DIR&=0xfD;//p1.1
  P1REN|=0X02;
  P1OUT|=0X02;
}
void LED_init(void)
{
  P1DIR|=0x01;
  P1OUT|=0x01;
}
void delay(int i)
{
  unsigned int a,b;
  for(a=0;a<=i;a++)
    for(b=0;b<=70;b++);
}
#pragma vector=TIMER0_A0_VECTOR 
__interrupt void TIMER0_A0_ISR(void)
{

/*TA0CCTL0&=0xfe;
TA0CCTL0 = CCIE;
TA0CCR0=32000;*/
TA0CCTL0&=0xfe;
P1OUT^=0x01;
count+=1;
}
