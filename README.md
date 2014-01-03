selenide-selenium-junit4
========================

selenide+selenium+junit4


testRightMouseMenu_IE.java
{code}
import org.junit.Test;
import org.openqa.selenium.By;

import com.codeborne.selenide.WebDriverRunner;
import static com.codeborne.selenide.Navigation.open;
import static com.codeborne.selenide.Navigation.baseUrl;
import static com.codeborne.selenide.WebDriverRunner.takeScreenShot;
import static org.junit.Assert.*;
public class testRightMouseMenu_IE {
	@Test
	public void Test_Grid_Menus_S1_IE() throws Exception {
		baseUrl = Env.getServerUrl();
		System.setProperty("webdriver.ie.driver", "c:\\tools\\IEDriverServer.exe");
		WebDriverRunner.browser = "ie";
		open("");
		// maxmize the ff browser
		WebDriverRunner.getWebDriver().manage().window().maximize();
		// right click on search textfield
		QxExt
				.rightClick(By
						.xpath("//*[@id='lst-ib']"));
		Thread.sleep(5000);

		
		//note: if manually run on FF , you'll see the effect image  build\reports\tests\expectImage.png
		//note: run this test with FF ,at run time, menu will show on the left top of the browser, but after take a screenshot ,no menu show up (x)
		// Make a snapshot should show normal menu, but infact on the screenshot there is no menu,refer image under build\reports\tests\RightMouseMenu_FF_actual
		takeScreenShot("RightMouseMenu_IE");
		Thread.sleep(5000);
		//compare the screenshot with expected one 
		boolean test2;
       //cut image, then compare
		test2=QxExt.image_compare("","RightMouseMenu_IE_expect.png", "RightMouseMenu_IE.png", "RightMouseMenu_IE_cuted.png",0, 121, 1024, 700);

		Thread.sleep(8000);
		//assesrt compare result
		assertEquals(test2,true);
		Thread.sleep(2000);
	}
	

}

{code}

Env.java
{code}



public class Env {
	static String getServerUrl() {

    	return "http://www.google.com.hk/";
    	
	}

	
}

{code}
QxExt.java
{code}


import static com.codeborne.selenide.WebDriverRunner.getWebDriver;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.security.MessageDigest;

import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.interactions.Action;
import org.openqa.selenium.interactions.Actions;



public class QxExt {
	
	

	  public static void rightClick(By by) {
	        WebElement element = getWebDriver().findElement(by);
	        Actions builder = new Actions(getWebDriver());
	        Action rClick = builder.contextClick(element).build();
	       
	        rClick.perform();
	    }
		public static boolean image_compare(String SQA,String imag_expect,String img_actual,String img_cut, int left, int top, int width, int height) throws Exception {
			String img1_expected;
			String img1_actualRunTime;
			String img1_cuted;
			boolean compareRs;
			
			String testpath=new File("").getAbsolutePath();
  
			//run on local -- by seperate test
			img1_expected = testpath+"/image/"+SQA+imag_expect;	
		    img1_actualRunTime = testpath+"/build/reports/tests/"+img_actual;
		    img1_cuted = testpath+"/image/"+SQA+img_cut;
		    
		
		    
		    TestCutImage.readUsingImageReader(img1_actualRunTime, img1_cuted,left, top, width, height);
		    Thread.sleep(5000);
			//compare
			compareRs = QxExt.compare(img1_expected, img1_cuted);
			Thread.sleep(5000);
		//	for take screenshot
//	        compareRs=true;
			//return compare result
			return compareRs;
		}
		public static boolean compare(String imagePath1,String imagePath2) throws Exception{

	        String img1 = md5(getByte(imagePath1));
	        String img2 = md5(getByte(imagePath2));
	        
	        if(img1.equals(img2))
	         //System.out.println("same image");
	        return true;
	      else
	         //System.out.println("different images");
			return false;
	      }
		 /**
	     *
	     * @param name
	     * @return
	     */
	    public static byte[] getByte(String name){
	    //
	        File file = new File(name);
	        byte[] b = new byte[(int) file.length()];
	        System.out.println(file.length());
	        try{
	            InputStream in = new FileInputStream(file);
	            try {
	     in.read(b);
	    // System.out.println(in.read());
	    } catch (IOException e) {
	     e.printStackTrace();
	    }
	        }catch (FileNotFoundException e){
	            e.printStackTrace();
	            return null;
	        }
	        return b;
	    }
	    
	  //compare image method will use 3 method compare,md5 and getByte
	    public static String md5(byte[] s){
	        //16
	        char hexDigits[] = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd','e', 'f' };
	        try{
	            byte[] strTemp = s;
	            MessageDigest mdTemp = MessageDigest.getInstance("MD5");
	            mdTemp.update(strTemp);
	            byte[] md = mdTemp.digest();
	            int j = md.length;
	            char str[] = new char[j * 2];
	            int k = 0;
	            //
	            for (int i = 0; i < j; i++){
	                byte byte0 = md[i];
	                str[k++] = hexDigits[byte0 >>> 4 & 0xf];
	                str[k++] = hexDigits[byte0 & 0xf];
	            }
	            return new String(str);
	        }
	        catch (Exception e){
	            return null;
	        }
	    }

    
}

{code}
TestCutImage.java
{code}


import java.awt.Rectangle;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

import java.util.Iterator;

import javax.imageio.ImageIO;
import javax.imageio.ImageReadParam;
import javax.imageio.ImageReader;
import javax.imageio.stream.ImageInputStream;

/**
*
*date:Oct 23, 2009
*time:5:24:00 PM
*
*
*/
public class TestCutImage
{

    
    /** 
     * read image files
     * use ImageReader 
     * @param imgPath 
     * @throws IOException 
     */ 
    public static void readUsingImageReader(String imgPath,String cutedImage ,int indexX,int indexY,int Width, int Height) throws IOException{ 
           
           //  get image reader container
           Iterator readers = ImageIO.getImageReadersByFormatName("png"); 
           System.out.println(readers); 
           ImageReader reader = (ImageReader)readers.next(); 
           System.out.println(reader); 
           
           //  get image reader stream
           File file = new File(imgPath);
           byte[] b = new byte[(int) file.length()];
           System.out.println(file.length());
           InputStream source = new FileInputStream(file);
      
           //InputStream source=this.parseImagePath(ImageDemo.SRC_FILE); 
           ImageInputStream iis = ImageIO.createImageInputStream(source); 
           
           reader.setInput(iis, true); 
           
           //  image parameters
         
           ImageReadParam param = reader.getDefaultReadParam(); 
           int imageIndex = 0; 
           int half_width = reader.getWidth(imageIndex)/2; 
           int half_height = reader.getHeight(imageIndex)/2; 
//         Rectangle rect = new Rectangle(60, 60, half_width, half_height); 
           //Rectangle rect = new Rectangle(0, 109, 1024, 700); 
           Rectangle rect = new Rectangle(indexX, indexY, Width, Height); 
           
           param.setSourceRegion(rect); 
    
           BufferedImage bi = reader.read(0,param);              
           
           ImageIO.write(bi, "png", new File(cutedImage));    
           
           
    } 

}
{code}
