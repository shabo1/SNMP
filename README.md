# SNMP

import java.net.*;
import java.io.*;

class Dgram 
{
  public static DatagramPacket toDatagram(byte buf[], InetAddress destIA, int destPort) 
   {
    return new DatagramPacket(buf, buf.length,destIA, destPort);
   }
  public static String toString(DatagramPacket p)
   {
    return new String(p.getData(), 0, p.getLength());
   }
 } 

public class sncli
{
  private DatagramSocket s;
  private InetAddress hostAddress;
  private byte[] buf = new byte[1000];
  private DatagramPacket dp =new DatagramPacket(buf, buf.length);
  private DatagramPacket dp1 = new DatagramPacket(buf, buf.length);
 

  public sncli() 
  {
   
    try  
    {
      s = new DatagramSocket(161);      
	
      hostAddress =InetAddress.getByName("127.0.0.0");
    } 
    catch(UnknownHostException e) 
    {
      System.err.println("Cannot find host");
      System.exit(1);
    } 
    catch(SocketException e) 
    {
      System.err.println("Can't open socket");
      e.printStackTrace();
      System.exit(1);
    }
    System.out.println("sncli starting");
  }
  public void run() 
  {
    try {
	byte kk[] = new byte[39];
	kk[0]=0x30;
	kk[1]=0x25;

	kk[2]=2;
	kk[3]=1;
	kk[4]=0;

	kk[5]=4;
	kk[6]=6;
	kk[7]='p';
	kk[8]='u';
	kk[9]='b';
	kk[10]='l';
	kk[11]='i';
	kk[12]='c';

	kk[13]=(byte)0xA0;
	kk[14]=0x18;

	kk[15]=2;
	kk[16]=1;
	kk[17]=1;

	kk[18]=2;
	kk[19]=1;
	kk[20]=0;

	kk[21]=2;
	kk[22]=1;
	kk[23]=0;

	kk[24]=0x30;
	kk[25]=0xd;

	kk[26]=0x30;
	kk[27]=0xb;
//Object ID
	kk[28]=6;
	kk[29]=7;
	kk[30]=0x2b;
	kk[31]=6;
	kk[32]=1;
	kk[33]=2;
	kk[34]=1;
	kk[35]=1;
	kk[36]=3;
	kk[37]=5;
	kk[38]=0;
      
	s.send(Dgram.toDatagram(kk, hostAddress,161));
        
	for(int i=0;i<39;i+=3)
	System.out.println(i+">"+kk[i]+"      "+(i+1)+">"+kk[i+1]+"   "+(i+2)+">"+kk[i+2]);

	System.out.println("Message sent-->"+kk+". Waiting for reply.................");
            s.receive(dp1);
	System.out.println("Recvd..........");
            byte[] kk1=dp1.getData();
	
	for(int i=0;i+3<dp1.getLength();i+=3)
System.out.println(i+">"+kk1[i]+"      "+(i+1)+">"+kk1[i+1]+"     "+(i+2)+">"+kk1[i+2]);
System.out.println(kk1.length);
String rcvd = "Client #"  + ", rcvd from " + dp1.getAddress() + ", " + dp1.getPort() + ": " +Dgram.toString(dp1);
        System.out.println(rcvd);
     } 
     catch(IOException e) 
     {
      e.printStackTrace();
      System.exit(1);
     }
  }
  public static void main(String[] args) {
         new sncli().run();
  }
}
