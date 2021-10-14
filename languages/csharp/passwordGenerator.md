# Password generation

In .NET Core and above, .NET does not have native string password generation. Below is some code to allow you to generate string passwords:

``` csharp

using System;
using System.Security.Cryptography;
using System.Text;
using System.Linq;
					
public class Program
{
	public static void Main()
	{
        //Cryptographically secure random generator
		using var randomNumberGenerator = new RNGCryptoServiceProvider();
        
        //Where we will store the password temporarily
        var passwordBytes = new byte[512];
        
        //Fill the password buffer with random bytes
        randomNumberGenerator.GetNonZeroBytes(passwordBytes);
        
        //Filter the random bytes to only ASCII charaters in the UTF8 specification, then turn that into a string.
        //There is about a 36% chance any one single byte will be in that range. By setting the number of bytes to 
        //be generated to 512, that should get you a password length of on average 188 char long.
        var password = Encoding.UTF8.GetString(passwordBytes.Where(x => x > 33 && x < 127).ToArray());
		
		Console.WriteLine(password);
	}
}

```