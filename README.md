import java.util.*;
import java.io.*;
class SpamDetection
{
	HashMap<String,Integer>hamword;//to store the count of hamwords
	HashMap<String,Integer>spamword;//to store the count of spamwords
	int tot;
	double pHam;//probablity of ham
	double pSpam;//probablity of spam
	Scanner scan;
	FileInputStream finp;
	void test()throws IOException
	{
		finp=new FileInputStream("test.text");
		scan=new Scanner(finp);
		hamword=new HashMap<String,Integer>();
		spamword=new HashMap<String,Integer>();
		int spam1=0;
		int ham1=0;
		while(scan.hasNext())
		{
			StringTokenizer token=new StringTokenizer(scan.nextLine());
			token.nextToken();
			String type=token.nextToken();
			if(type.equals("spam"))
			spam1++;
			else
			ham1++;
			while(token.hasMoreTokens())
			{
				String word=token.nextToken();
				if(type.equals("spam"))
				{
					int tcount=Integer.parseInt(token.nextToken());
					if(spamword.containsKey(word))
					spamword.put(word,spamword.get(word)+tcount);
					else
					spamword.put(word,tcount);
				}
				else
				{
				int tcount=Integer.parseInt(token.nextToken());
					if(hamword.containsKey(word))
					hamword.put(word,hamword.get(word)+tcount);
					else
					hamword.put(word,tcount);	
				}
				}
			}
			tot=spam1+ham1;
			pHam=(1.0*ham1)/tot;
			pSpam=(1.0*spam1)/tot;
		}
		double probability(StringTokenizer token,int flag)//flag 1 represents the probability of of the mail from spam else ham
	{
		
		double result = 1.0;
		
		while(token.hasMoreTokens())
		{
			String word = token.nextToken();
			
			int sCount = getCount(word,1);
			int hCount = getCount(word,0);
			
			int count = Integer.parseInt(token.nextToken());
			
			
			double tt = sCount + hCount;
			
			double forSpam = sCount/tt;
			double forHam = hCount/tt;
			
			forSpam = Math.pow(forSpam,count);
			forHam = Math.pow(forHam,count);
			//System.out.println(forHam);
			
			if(flag==1)
				result = result * forSpam;
			else if(flag==0&&hCount!=0)
				result = result * forHam;
		}
		
		//System.out.println(result);
		return result;
	}
	
	int getCount(String word,int flag)
	{
		if(flag==0)
		{
			if(hamword.containsKey(word))
				return hamword.get(word);
			else
				return 0;
		}
		else
		{
			if(spamword.containsKey(word))
				return spamword.get(word);
			else
				return 0;
		}
		
	}
	
	int train(StringTokenizer mail,String s)
	{

		double ham  = probability(mail,0);
		
		StringTokenizer token = new StringTokenizer(s);
		token.nextToken();
		token.nextToken();
		
		double spam = probability(token,1);
		
		double tP = spam*pSpam + ham*pHam;

		
		//System.out.println(spam);
		
		if(spam==0.0&&ham==0.0)return 0;//ham
		else
		{
			double tHam = (ham*pHam*1.0)/tP;
			double tSpam = (spam*pSpam*1.0)/tP;
			
			//System.out.println(tHam + " " + tSpam);
			
			if(tSpam>tHam)
				return 1;
			else
				return 0;
		}
	}
		public static void main(String args[])throws IOException
		{
			SpamDetection filter=new SpamDetection();
			filter.test();
			FileInputStream fin=new FileInputStream("test.txt");
			Scanner scan=new Scanner(fin);
			int successful=0;
			int total=0;
			while(scan.hasNext())
			{
				String message=scan.nextLine();
				StringTokenizer token=new StringTokenizer(message);
				token.nextToken();
				String ans=token.nextToken();
				int result=filter.train(token,message);
				if(result==1 && ans.equals("spam"))
				successful++;
			else if(result==0 &&ans.equals("ham"))
				successful++;
				
			total++;
		}
		

		double accuracy = ((successful*1.0)/total)*100.0;
		System.out.println(accuracy);
		
	}
}
				
			
