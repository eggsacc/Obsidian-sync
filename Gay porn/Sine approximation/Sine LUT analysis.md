
Created <font style="color:tomato; font-family:Consolas;">18-10-2024</font>

Tags: #arduino 

Related: [[Optimizing sine LUT]]

****

## Error calculation

Let's examine the accuracy and speed of the `_sin()` function we created.

Firstly, we will evaluate the accuracy of our sine lookup table and compare the output from `_sin()`  to that of the standard `sin()` function in the C library.

The code to calculate the difference between the `_sin()` and `sin()` value for a range of angles `i` is given below:

````c
int main()

{
	float sumDiff = 0;
	int samples = 0;
	
	for(float i = 0; i < _PI_2; i+=0.01)
	{
		float csin = sin(i);
		float usin = _sin(i);
		float difference = csin - usin;
		        
		sumDiff += difference;
		samples++;
		        
		printf("Angle:%f, sin():%f, _sin()%f, Difference:%f\n", 
				i, csin, usin, difference);
	}
	
	printf("Mean difference: %f\n", sumDiff / samples);
	
	return 0;
}
````

The difference (error) is plotted against the input angle between $0-\frac{\pi}{2}$ in Google sheets.

![[Error against Angle (1).svg#invert]]

![[Z-SCORE DISTRIBUTION.svg#invert]]


## Error analysis

- **Max. error:** +0.000167 (0.0167%)
- **Min. error:** +0.000026 (0.0026%)
- **Mean error:** +0.00009078 (0.009078%)
- **Standard deviation:** 0.00002936

In the code, the error value is `sin() - _sin()`. Since the mean error is non-zero and all the error values are positive, we can conclude that our `_sin()` function yields a constant under-estimate of the actual value. We can add the mean value of 0.00009078 to correct for the bias, but the error is too small (<0.01%) to be worth the extra effort and computational power.

The histogram of Z-scores also shows that most of the output values lie within 1 Standard Deviation (S.D) from the mean, hence the function is sufficiently precise.

## Speed analysis

To compare the execution speed between the `sin()` and `_sin()` functions, we iterate through a `for()` loop and calculate the sine values of a range of angles from $0-\frac{\pi}{2}$, timing the number of microseconds elapsed for the loop to finish executing.

````c
double _2_PI = 6.28318530718;
float _PI_2 = 1.57079632679;
float DOWNSCALER = 0.00003051757;

int16_t sineLUT[65] = {0, 804, 1607, 2410, 3211, 4011, 4807, 5601, 6392, 7179, 7961, 8739, 9511, 10278, 11038, 11792, 12539, 13278, 14009, 14732, 15446, 16150, 16845, 17530, 18204, 18867, 19519, 20159, 20787, 21402, 22004, 22594, 23169, 23731, 24278, 24811, 25329, 25831, 26318, 26789, 27244, 27683, 28105, 28510, 28897, 29268, 29621, 29955, 30272, 30571, 30851, 31113, 31356, 31580, 31785, 31970, 32137, 32284, 32412, 32520, 32609, 32678, 32727, 32757, 32767};

float _sin(float angle)
{
        uint16_t i = (uint16_t)(angle / _2_PI * 65536.0f);
        int8_t frac = i & 0xFF;
        i = (i >> 8) & 0xFF;

        int t1, t2;

        if(i < 64){
            t1 = (int)sineLUT[i];
            t2 = (int)sineLUT[i+1];
        }
        else if(i < 128){
            t1 = (int)sineLUT[128 - i];
            t2 = (int)sineLUT[127 - i];
        }
        else if(i < 192){
            t1 = -(int)sineLUT[i - 128];
            t2 = -(int)sineLUT[i - 127];
        }
        else{
            t1 = -(int)sineLUT[256 - i];
            t2 = -(int)sineLUT[255 - i];
        }

    return DOWNSCALER * (t1 + (((t2 - t1) * frac) >> 8));
}

void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);

  // ***************** built-in sin() *****************

  unsigned long time = micros();

  for(float i = 0; i < _PI_2; i += 0.01)
  {
    volatile float value = sin(i);
  }

  Serial.print(micros() - time);
  Serial.println(" microseconds for built-in sin()");

  // ***************** sine lookup *****************

  time = micros();

  for(float i = 0; i < _PI_2; i += 0.01)
  {
    volatile float value = _sin(i);
  }

  Serial.print(micros() - time);

  Serial.println(" microseconds for sine LUT");
}

void loop() {}
````

**Time taken:**

````
18120 microseconds for built-in sin()
10592 microseconds for sine LUT
````

Calculating 157 sine values using the sine lookup table shows a 42% increase in speed over the default `sin()` function.

Overall, the `_sin()` function is considerably faster, has good precision and produces consistent results, hence it is a suitable approximation of the sine function.

