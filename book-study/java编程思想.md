# java编程思想
1. 基本数据类型初始化（java给予默认值），只有在变量为类成员变量时，java才确保给其默认值。
2. static应用场景：为某特定区域分配内存空间而不考虑有多少个对象，甚至没有对象；某个方法不与包含它的类的任何对象关联在一起，也就是说，即使没有创建对象，也能调用。可以用类对象和类数据来形容被static修饰的。
3. static的重要用法就是在不创建任何对象的情况下调用，这对main方法很重要，因为它是运行一个应用时的入口点。
4. java中的数据对象是存在哪里
	- 寄存器：最快的存储区，但是不能直接操作，且数量有限，所以按需分配。
	- 堆栈：位于通用RAM（随机访问存储器），通过堆栈指针来分配存储区域。指针向下移动，则分配新的内存。指针向上，则释放内存。java系统需要知道存放在堆栈中的项的生命周期，以便控制指针的移动，所以**java对象**存放于堆中，**java对象的引用**和**基本数据类型**存放于堆栈中。
	- 堆：通用内存池（同样位于RAM）中，用来存储**java对象**。堆与堆栈的区别便在于，堆不需要知道存储的数据需要存活多长时间。，
	- 常量存储：常量值通常直接存放在程序代码内部，这样做是安全的，因为它们永远不会被改变。有时，在嵌入式系统中，常量本身会和其他部分分割离开，所以在这种情况下，可以选择将其放在ROM中。
	- 非RAM存储：若数据完全独立于一个程序之外，则程序不运行时仍可存在，并在程序的控制范围之外。其中两个最主要的例子便是“流式对象”和“固定对象”。https://blog.csdn.net/xuguoli_beyondboy/article/details/41939987
5. 单例模式	
```java
	public class Soup{

		private static Soup sp = new Sout();
		
		public static Soup access(){
			return sp;
		}
	}
```
为什么说单例模式只能产生一个对象呢？
sp对象是私有的，说明只能通过方法access来访问。static表明sp是类成员，在第一次使用Soup类时就会被创建，之后便不会被创建了，因为所有对象均共享。所有在Soup的整个生命周期当中，有且仅有一个sp对象。

6. 向上转型
现有继承关系如下：student extends people
向上转型是由一个专用类型向通用类型转型，在例子中，student为people的一个超集，它可能含有比基类people更多的方法，但它必须具备基类中所包含的全部方法。
向上转型的过程中，唯一可能发生的便是丢失方法，而不是获取它们，这就是为什么编译器在**未曾明确表示转型**和**未指定特殊标记**的情况下，仍允许向上转型的原因。

7. final变量
```
	public static Soup sp = new Sout(12)
```
用final变量修饰引用，表示该引用无法在堆中指向新的对象，但这并不代表无法改变它的数值。

8. 空白final
空白final是指被申明为final又未给定初值的域，比如：
```java
	public class BlankFinal{

		private final int j;
		private final Poppet p;
		
		public BlankFinal(){
			j =1;
			p = new Poppet();
		}

		public BlankFinal(int x){
			j = x;
			p = new Poppet(x);
		}	
	}
```
无论什么情况，编译器都会确保空白final在使用前被初始化。为此，一个final域可以根据对象的不同，却又保持其很定不变的特性。

9. final方法
final方法存在的意义就是，不允许子类覆盖基类的方法。用final来修饰基类的private方法，是没用出的，因为private方法除了类本身，外部均访问不了，所以更不存在覆盖了。所以private方法是被隐式指定为final的。
10. final类
所有被指定为final的类，即表明该类不可继承，这么做的理由是不希望有人改动该类，或者处于安全考虑。且final类中所有的方法均被隐式指定为final，因此无法覆盖它们。final类的域规则和常规类相同。
11. 继承与初始化
现有基类A，子类B，且A中包含static变量，那么在加载子类B时，编译器会注意到它有一个基类，于是它便于优先加载基类的static变量。不管是否B是否打算产生一个对象，这都要发生的。
12. 成员变量和方法的加载顺序
	- 成员变量优先于方法（包括构造器）
	- static变量优先于staic方法
	- 变量优先于方法
	- static方法优先于非static方法
	- static代码块优先于非static代码块
	- 代码块优先于方法
	- 成员变量优先于代码块
	- 基类优先于子类
	- 基类构造函数优先于子类构造函数
	- 遵循一个类一个类的顺序加载

13. 构造器内部的多态方法行为：一般情况，动态绑定的调用是在运行时决定的，因为对象无法决定它是属于方法所在的那个类，还是那个类的导出类（存在基类和导出类，方法可以被覆盖）。如果在构造器内部调用正在构造对象的某个动态方法，就要用到方法被覆盖后的定义，举个例子：
```
class Glyph{
	void draw{print("Glyph.draw()");}
	Glyph(){
		print("Glyph() before draw()");
		draw();
		print("Glyph() after draw()");
	}
}
class RoundGlyph extends Glyph{
	prvate int radius = 1;
	
	RoundGlyph(int radius){
		radius = r ;
		print("RoundGlyph.RoundGlyph().radius = " + radius);
	}
	void draw{print("RoundGlyph.draw().radius = " + radius);}	
}
public class PolyConstructors{
	public static void main(String[] args){
		new RoundGlyph(5);
	}
}
output:
Glyph() before draw()
RoundGlyph.draw().radius = 0
Glyph() after draw()
RoundGlyph.RoundGlyph().radius = 5
```
Glyph.draw()方法被覆盖，而这个覆盖是在RoundGlyph中发生的。而且在Glyph的构造器调用draw方法时，会发现radius不是默认的初始值1，而是0，这与初始化顺序有关：
1）在其他任何事物发生之前，将分配给对象的存储空间初始化为二进制的零。
2）如前所述那样调用基类，此时，调用被覆盖后的draw方法（会在RoundGlyph构造器之前被调用），由于步骤1的缘故，会发现radius的值为0.
3）按照声明的顺序调用成员的初始化方法
4）调用导出类的构造器方法
在构造器中应避免调用其他方法，唯一能够安全调用的便是基类中的final方法（同样适用于private方法，它们自动属于final），因为它们无法被覆盖。