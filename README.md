# TGASharpLib + Procedural seamless noise texture generator + intmain.xyz Georgiy Chipunov code..
https://www.codeproject.com/Articles/838511/Procedural-seamless-noise-texture-generator
For Crimewars and lumberyard projects.


## Description
1. TGASharpLib is a FREE, OpenSource .NET library for reading and writing TGA files.
2. Library contain only 1 file: "TGASharpLib.cs", that's why it can be used in any standalone project!

## Link
* https://gitlab.com/Alex_Green/TGASharpLib
* https://github.com/ALEXGREENALEX/TGASharpLib

## License:
**MIT**

## Supported convertions and features:
1. Bitmap, Byte[], File, Stream <-> TGA.
2. Flip TGA image.
3. Get, Create, Update, Delete PostageStamp image (something like Thumbnail, preview image).
4. Supported: RLE, Alpha, PAlpha, New TGA Headers (Old, New, Old -> New TGA Type Convertion), Tags.
and many others features...

## Author
Zelenskyi Alexandr (Зеленський Олександр)
If you will found some bugs, please write me: alex.green.zaa.93@gmail.com

##MODDER
Georgiy Chipunov (dion't have cyric installed)
gchipunov@gmail.com



#info

<h2>Update</h2>

<p>I used the basis of this article in the tool you can find :&nbsp;<a href="https://sites.google.com/site/noiselab15/">https://sites.google.com/site/noiselab15/</a></p>

<h2>Introduction</h2>

<p>For some stuff I get involved concerning run time planets generation I faced procedural seamless texture generation. To create a planet at run time is foundamental to manage seamless texture. A seamless texture can work as greyscale highmap and/or landscape texture and/or atmosferic effects.</p>

<p>Searching the web I found some well known tecniques like perlin noise (<a href="http://en.wikipedia.org/wiki/Perlin_noise">http://en.wikipedia.org/wiki/Perlin_noise</a>) or fractal noise.</p>

<p>So I decided to arrange a c# "sandbox" small project to experiment some texture generation algorithms, to test performance , and so on. In particular I implemented a Perlin noise generator using resources from web (references will follow), after that I've created some recoursive algoritms that can reach, in my opinon, sadisfactory effects compared to Perlin, at a good performance cost. &nbsp;Here I'm going to share some stuff.&nbsp;</p>

<p>&nbsp;</p>

<h2>Background</h2>

<p>I implemented Perlin noise studing these web sources : <a href="http://www.sepcot.com/blog/2006/08/PDN-PerlinNoise2d">http://www.sepcot.com/blog/2006/08/PDN-PerlinNoise2d</a>&nbsp;and&nbsp;<a href="http://freespace.virgin.net/hugo.elias/models/m_perlin.htm">http://freespace.virgin.net/hugo.elias/models/m_perlin.htm</a>&nbsp;. I'm not going to explain Perlin noise , first reason I'm not so smart to explain something I merely understand better than the links above.</p>

<p>Another (this time easy) thing usefull before going ahead is to know what a grayscale image is and how usefull it is for define a heightmap. At the same time is usefull to know how to use the alpha chanell of an image , for example to add clouds on a predefined sky.</p>

<p>The basic idea is to draw on a random sub region of the image a light grey rectangle&nbsp;(let's call it a "box") covering the sub region. After that we calculate n random point near the center of first box , we reduce box size (i.e by half) and we repeat for the n small boxes. Every time we draw a box we add its grey delta to the preexistents pixels.</p>

<p>In the sequence below we start with 2 recursion calls and we end with 20 recursion call. For the last image we repeat it 7 times and we reduce the grey delta to make vanishing the bigger boxes.</p>

<p><img alt="" src="https://www.codeproject.com/script/Membership/Uploads/1172116/noisetest1.jpg" style="width: 873px; height: 793px"></p>

<p>Finaly, the textures created are seamless and perfectly tileable</p>

<h2>Using the code</h2>

<p>The main recursive method of Contoli noise is very simple :</p>

<pre lang="cs"><span class="code-comment">//</span><span class="code-comment"></span>
<span class="code-comment">//</span><span class="code-comment"> CONTOLI NOISE</span>

<span class="code-keyword">private</span> <span class="code-keyword">void</span> printQuad(<span class="code-keyword">float</span> [,] Values, <span class="code-keyword">int</span> wid, <span class="code-keyword">int</span> hei,<span class="code-keyword">int</span> x, <span class="code-keyword">int</span> y, <span class="code-keyword">int</span> boxSize, <span class="code-keyword">int</span> deepness, <span class="code-keyword">int</span> delta) {

            <span class="code-keyword">if</span> (deepness <span class="code-keyword">&gt;</span> <span class="code-digit">0</span> &amp;&amp; boxSize&gt;=<span class="code-digit">1</span>)
            {
                <span class="code-keyword">for</span> (<span class="code-keyword">int</span> i = -boxSize/2; i <span class="code-keyword">&lt;</span> boxSize/2; i++)
                {
                    <span class="code-keyword">for</span> (<span class="code-keyword">int</span> j = -boxSize / <span class="code-digit">2</span>; j <span class="code-keyword">&lt;</span> boxSize / <span class="code-digit">2</span>; j++)
                    {
                            <span class="code-comment">/*</span><span class="code-comment">seamless management start*/</span>
                            <span class="code-keyword">int</span> pixX = (x + i) % wid;
                            <span class="code-keyword">int</span> pixY = (y + j) % hei;
                            <span class="code-keyword">if</span> (pixX <span class="code-keyword">&lt;</span> <span class="code-digit">0</span>)
                                pixX = wid + pixX;
                            <span class="code-keyword">if</span> (pixY <span class="code-keyword">&lt;</span> <span class="code-digit">0</span>)
                                pixY = wid + pixY;
                            <span class="code-comment">/*</span><span class="code-comment">seamless management end*/</span>

                            Values[pixX, pixY] = Values[pixX, pixY] + delta; <span class="code-comment">//</span><span class="code-comment"> add value</span>

                            Iterations++;
                    }
                    <span class="code-keyword">int</span> xx = r.Next(x, x + boxSize);
                    <span class="code-keyword">int</span> yy = r.Next(y, y + boxSize);
                    printQuad(Values, wid, hei, xx, yy, boxSize / <span class="code-digit">2</span>, --deepness, delta);
                }
            }

        }

<span class="code-comment">//</span><span class="code-comment"></span></pre>

<p>Where:&nbsp;</p>

<p><code>Values</code>&nbsp;is the bidimensional array of float representing the image pixels</p>

<p><code>wid </code>and <span style="color: rgb(153, 0, 0); font-family: Consolas, &quot;Courier New&quot;, Courier, mono; font-size: 15px">hei&nbsp;</span>are the image dimension</p>

<p><code>x&nbsp;</code>and&nbsp;<span style="color: rgb(153, 0, 0); font-family: Consolas, &quot;Courier New&quot;, Courier, mono; font-size: 15px">y&nbsp;</span>are the center point coords of the box</p>

<p><code>boxSize</code>&nbsp;is the size of the rectangle</p>

<p><code>deepness</code>&nbsp;how deep in recursion are now</p>

<p><code>delta</code>&nbsp;how many grey scale we add over existing values. (It represent the delta height)</p>

<p>Stopping the recursion :&nbsp;</p>

<pre lang="cs"><span class="code-keyword">if</span> (deepness <span class="code-keyword">&gt;</span> <span class="code-digit">0</span> &amp;&amp; boxSize&gt;=<span class="code-digit">1</span>)
</pre>

<p>we stop the recursion if we are too deep or if box size reachs zero.</p>

<p>To call it ;</p>

<pre>r = getrRandom(); //r is public Random class accessed by all methods
for (int nr = 0; nr &lt; N_Repetitions; nr++) {
    //get a random point
    int x = r.Next(0, wid - 1);
    int y = r.Next(0, hei - 1);

    printQuad(Values, wid, hei, x, y, box_size, Godeep, d);

}
updBitmap(Values, tmp, grayScale.Checked);
pictureBox1.Image = tmp;
pictureBox1.Refresh();
</pre>

<p><code>updBitmap</code> converts the Values array in a greyscaled bitmap.</p>

<p><code>getRandom</code>&nbsp;initialize the random number generator used inside <code>printQuad</code>.</p>

<p><code>N_Repetitions</code>&nbsp;is self explained.</p>

<p>The application:</p>

<p><img alt="" src="https://www.codeproject.com/script/Membership/Uploads/1172116/noiseForm1.jpg"></p>

<p>The Contoli Noise Methods:</p>

<p>Quad : the simplest already discussed;</p>

<p>Circ : draws circles instead of rectangles, we get a better effect keeping recursion deepness low;</p>

<p>Func ; draws a 2 variable function instead of rectangles. Actualy the function is a Torus ( <a href="http://en.wikipedia.org/wiki/Torus">http://en.wikipedia.org/wiki/Torus</a>) .</p>

<p>Quad (left) VS Circ (right):&nbsp;</p>

<p><img alt="" src="https://www.codeproject.com/script/Membership/Uploads/1172116/quadVscirc.jpg"></p>

<p>The torus example: 1 recursion deepnes vs 33 recursion deepnes.&nbsp;</p>

<p><img alt="" src="https://www.codeproject.com/script/Membership/Uploads/1172116/torus.jpg" style="width: 669px; height: 578px"></p>

<p>General note: when incrementing "recursion" value is good to decrement "delta color" value.</p>

<p>Using the <code>printFunc </code>code.&nbsp;</p>

<pre>//TORUS
f func  = (x1,y1) =&gt; (float)Math.Sqrt((0.4f * 0.4f - Math.Pow((0.6f - Math.Sqrt(x1 * x1 + y1 * y1)), 2)));//TORUS

printFunc(Values, wid, hei, x, y, box_size, Godeep, d,func);
useValueArray = true;
break;
</pre>

<p><code>printFunc</code> accepts a function <code>f</code> delegate. If you don't like TORUS You can redefine it the way you want.</p>

<p>Example for paraboloid:</p>

<pre>f func =  (x1,y1) =&gt; 1f + (float) ((x1*x1)/1f + (y1*y1)/1f) * -1f; //PARABOLOID</pre>

<p>In the "Distance alg." section I present a set of algorithms to generate effects over a bitmap using tecniques based on distances from a set of random points. It's work in progress.</p>

<h2>Voronoi diagram</h2>

<p>In the Voronoi section we can experiment some implementations of the procedural space tesselation, based on Voronoi idea. The basic idea is : 1) take a random set S of points on a plane; 2) every point "s" of S defines a region "r" on the plane where each point in the region "r" is the closest to point "s" (harder to explain tha understand) 3) use some random color (grayscale) to paint each region (or the region borders) 4) add some coding to keep all the stuff "seamless"</p>

<p>The examples:</p>

<p><img alt="" src="https://www.codeproject.com/script/Membership/Uploads/1172116/voronoi1.jpg"></p>

<p>The code:</p>

<pre lang="cs"><span class="code-keyword">private</span> <span class="code-keyword">void</span> Voronoi(<span class="code-keyword">float</span>[,] Values, <span class="code-keyword">int</span> wid, <span class="code-keyword">int</span> hei, <span class="code-keyword">int</span>[,] points)
{
    <span class="code-keyword">for</span> (<span class="code-keyword">int</span> i = <span class="code-digit">0</span>; i <span class="code-keyword">&lt;</span> wid; i++)
    {
        <span class="code-keyword">for</span> (<span class="code-keyword">int</span> j = <span class="code-digit">0</span>; j <span class="code-keyword">&lt;</span> hei; j++)
        {
            <span class="code-keyword">float</span> minDist = Math.Min(wid,hei);
            <span class="code-keyword">int</span> minV = <span class="code-digit">0</span>;
            <span class="code-keyword">for</span> (<span class="code-keyword">int</span> p = <span class="code-digit">0</span>; p <span class="code-keyword">&lt;</span> points.GetLength(<span class="code-digit">0</span>); p++) {
                <span class="code-keyword">int</span> pX = points[p, <span class="code-digit">0</span>];
                <span class="code-keyword">int</span> pY = points[p, <span class="code-digit">1</span>];
                <span class="code-keyword">int</span> pV = points[p, <span class="code-digit">2</span>]; <span class="code-comment">//</span><span class="code-comment">0..255</span>
                <span class="code-sdkkeyword">Double</span> Dist1X = Math.Abs((i - pX));
                <span class="code-sdkkeyword">Double</span> Dist1Y = Math.Abs((j - pY));
                <span class="code-sdkkeyword">Double</span> Dist2X = wid - Dist1X;
                <span class="code-sdkkeyword">Double</span> Dist2Y = hei - Dist1Y;
                <span class="code-comment">/*</span><span class="code-comment">to grant seamless I take the min between distX and wid-distX
                 |                       |
                 |                       |     ----------- = Dist1X
                 |...i-----------X.......|     ..........  = Dist2X
                 |                       |
                 */</span>
                Dist1X = Math.Min(Dist1X, Dist2X);
                <span class="code-comment">/*</span><span class="code-comment">to grant seamless I take the min between distY and hei-distY*/</span>
                Dist1Y = Math.Min(Dist1Y, Dist2Y);
                <span class="code-keyword">float</span> dist = (<span class="code-keyword">float</span>)Math.Sqrt(Math.Pow(Dist1X, <span class="code-digit">2</span>) + Math.Pow(Dist1Y, <span class="code-digit">2</span>));
                <span class="code-keyword">if</span> (dist <span class="code-keyword">&lt;</span> minDist) {
                    minDist = dist;
                    minV = pV;
                }
            }
            Values[i, j] = minV;
            Iterations++;
        }
    }
}
</pre>

<p>&nbsp;</p>

<h2>3D Noise</h2>

<p>Going 3d. I added a 3d implementation of contoli noise and voronoi noise. To test it simply push "Start 3d noise" , wait some time depending on the parameters , enjoy.</p>

<p><img alt="" src="https://www.codeproject.com/script/Membership/Uploads/1172116/3dTest.jpg"></p>

<p>Some explanation , in this case the 3rd dimension is TIME, so we get an "animated" noise. If you comapare&nbsp;<code>printQuad3D</code> with&nbsp;<code>printQuad</code> you'll find the 3rd dimension management. Obviosly, even the 3rd dimension is seamless, and let the animation to loop without cuts.</p>

<p>Animation demo links:</p>

<p><strong>Quad 3d noise:</strong>&nbsp;</p>

<p><a href="https://www.youtube.com/watch?v=cVnhmmPVSf8">https://www.youtube.com/watch?v=cVnhmmPVSf8</a></p>

<p><strong>Circ 3d noise:&nbsp;</strong></p>

<p><a href="https://www.youtube.com/watch?v=cFVYq_cS5C4">https://www.youtube.com/watch?v=cFVYq_cS5C4</a>&nbsp;</p>

<p><a href="https://www.youtube.com/watch?v=7qPrKBut6qY">https://www.youtube.com/watch?v=7qPrKBut6qY</a></p>

<p><strong>Voronoi animations</strong></p>

<p><a href="https://www.youtube.com/watch?v=GnR6dLPxvNc">https://www.youtube.com/watch?v=GnR6dLPxvNc</a></p>

<p><a href="https://www.youtube.com/watch?v=uihClJuwqz8">https://www.youtube.com/watch?v=uihClJuwqz8</a></p>

<p><a href="https://www.youtube.com/watch?v=TpkrNQe4guQ">https://www.youtube.com/watch?v=TpkrNQe4guQ</a></p>

<p><a href="https://www.youtube.com/watch?v=CfpY8saDa9U">https://www.youtube.com/watch?v=CfpY8saDa9U</a></p>

<p>&nbsp;</p>

<h2>History</h2>

<p>First version.</p>

<p>9-NOV-2014 : added VORONOI section.</p>

<p>12-NOV-2014 : added 3D Noise section.</p>

<p>14-NOV-2014 : added 3d noise demos youtube links</p>


						</div>
						

						<div class="float-right" style="margin:20px 0 0 10px;border:1px solid #ccc">
						<div class="msg-300x250" data-format="300x250" data-type="ad" data-publisher="lqm.codeproject.site" data-zone="Multimedia/General-Graphics/Bitmaps"  data-country='US' data-loadOnView='true'  data-tags='C#, Windows, .NET, Visual-Studio, Dev, Beginner, Intermediate, Advanced, VS2010, delegates, mathematics, .NET4.5,rating4.5'><noscript><a href="https://pubads.g.doubleclick.net/gampad/jump?iu=/6839/lqm.codeproject.site/Multimedia/General-Graphics/Bitmaps&sz=300x250&c=962803"><img src="https://pubads.g.doubleclick.net/gampad/jump?iu=/6839/lqm.codeproject.site/Multimedia/General-Graphics/Bitmaps&sz=300x250&c=962803"  width="300px" height="250px" target="_blank"/></a></noscript></div>
						</div>
                        
                        
						
						<h2>License</h2>
						<div id="LicenseTerms"><p>This article, along with any associated source code and files, is licensed under <a href="http://www.codeproject.com/info/cpol10.aspx" rel="license">The Code Project Open License (CPOL)</a></p></div>
						<h2>Share</h2>
				        <div class="share-list">
					        

<div id="ctl00_SocialLike_ShareaholicArea" class="shareaholic-canvas" data-app="share_buttons" data-app-id="28412723"></div>
				        </div> 
    			        
                        <h2 id="ctl00_AboutHeading">About the Author</h2>
						    

<div class="author-wrapper">

    <div class="pic-wrapper"> 
        <img id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_memberPhoto" class="profile-pic" src="https://codeproject.global.ssl.fastly.net/script/Membership/Images/member_unknown.gif" style="border-width:0px;transform:rotate(2deg);" />
    </div>

    <div class="container-member">  
        <b><a id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_memberProfileLink" class="author" href="/Members/acontoli">andrea contoli</a></b>

        <table>
        <tr>
            <td rowspan="2" nowrap valign="middle">
            
            </td>
            <td nowrap="nowrap">
                <div class="company">
                    <span id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_memberJobTitle">Software Developer</span>
	                <span id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_memberCompany"></span>
                </div>
            </td>
            <td rowspan="2" style="padding-left:15px">
                <a id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_FollowMember_FollowBtn" title="Have events related to this Member appear in your timeline" class="button unfollowed align-center invisible" class="invisible" data-enabletooltip="true" data-tooltip="Have the articles and posts from this member appear in your Timeline so you can stay up to date." style="display:none;">Follow<div class='tiny-text' style='margin-top:-3px;'>this Member</div></a>
	<a id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_FollowMember_UnfollowBtn" title="Unfollow this Member" class="button followed align-center invisible" class="invisible" data-enabletooltip="true" data-tooltip="Stop following this Member" style="display:none;">Unfollow</a>

            </td>
        </tr>
        <tr>
            <td>
                <span id="ctl00_AboutAuthorRptr_ctl00_AboutAuthor_memberLocation">Italy <img src="/script/Geo/Images/IT.gif" alt="Italy" width="16px" height="11px" /></span>
            </td>
        </tr>
        </table>
    </div>

    <div class="description">
        No Biography provided

        
    </div>

</div><br />
						
						

						<div class="clearfix"></div>

						<div style="padding-top:8px">
							
						</div>

					

				    
					</form>

				</div>

				
				<div class="bottom-promo"> 
				    <div class="msg-728x90" data-format="728x90" data-type="ad" data-publisher="lqm.codeproject.site" data-zone="Multimedia/General-Graphics/Bitmaps"  data-country='US' data-loadOnView='true'  data-tags='C#, Windows, .NET, Visual-Studio, Dev, Beginner, Intermediate, Advanced, VS2010, delegates, mathematics, .NET4.5,rating4.5,pos_bottom'><noscript><a href="https://pubads.g.doubleclick.net/gampad/jump?iu=/6839/lqm.codeproject.site/Multimedia/General-Graphics/Bitmaps&sz=728x90&c=962803"><img src="https://pubads.g.doubleclick.net/gampad/jump?iu=/6839/lqm.codeproject.site/Multimedia/General-Graphics/Bitmaps&sz=728x90&c=962803"  width="728px" height="90px" target="_blank"/></a></noscript></div>
				</div>
				
                
                

				
				

					<h2>Comments and Discussions</h2>
					<a class="float-left" name="_comments" id="_comments">&nbsp;</a><div id="_MessageBoardctl00_MessageBoard" onclick="return forumControl286884.SwitchMessage(event, null)">
<table id="ForumTable" class="forum relaxed" cellpadding="0" cellspacing="0">
<tr>
<td><table width="100%" border="0" cellpadding="3px" cellspacing="0">
<tr class="header1">
<td colspan="2" style="white-space:nowrap;"><div class="container">
<div class="button new-message compose float-left" onclick="forumControl286884.FireNew(this)">
<a rel="nofollow" href="/script/Forums/Edit.aspx?fid=1872579&amp;floc=/Articles/838511/Procedural-seamless-noise-texture-generator" name="Frm_HoverNL" target="_top">Add a Comment or Question&nbsp;</a>
</div><div class="admin-links float-left align-middle" style="width:90px;line-height:24px;margin-top:4px;">
&nbsp;<a href="/KB/FAQs/MessageBoardsFAQ.aspx"><img src="https://codeproject.global.ssl.fastly.net/images/help_sm.png" title="FAQ" alt="FAQ" style="border-width:0;width:16px;height:16px;vertical-align:middle;" /></a>
</div><div class="float-right">
<form action="/Search.aspx?sbo=fm" method="get" class="searchbar">
<input type="hidden" name="fid" value="1872579" /><input type="hidden" name="sbo" value="fm" /><input id="search_forumControl286884" type="search" class="search" name="qf" /><input type="image" src="https://codeproject.global.ssl.fastly.net/images/search.gif" />
</form>
</div><div class="tooltip email-alert" id="ctl00_MessageBoard_SubscribeDiv">
	<span class="nowrap medium-text">Email Alerts</span><div class="tooltip-flyout" style="width: 335px;margin-top: 24px;left: -270px;padding: 7px;">
		<b>Email me</b><input type="checkbox" id="ctl00_MessageBoard_SubscribeNew" /><label for="ctl00_MessageBoard_SubscribeNew">New Posts</label><input type="checkbox" id="ctl00_MessageBoard_SubscribeReplies" /><label for="ctl00_MessageBoard_SubscribeReplies">Replies to Posts</label><br><br><b>Send them</b><input type="radio" name="ctl00$MessageBoard_SubscribeSchedule" id="ctl00_MessageBoard_SubscribeInstant" /><label for="ctl00_MessageBoard_SubscribeInstant">Instantly</label><input type="radio" name="ctl00$MessageBoard_SubscribeSchedule" id="ctl00_MessageBoard_SubscribeDaily" /><label for="ctl00_MessageBoard_SubscribeDaily">Daily</label>&nbsp; <b>For</b> <select id="ctl00_MessageBoard_MessageType" class="tiny-text" style="width:80px;">
			<option value="0">
				All Types
			</option><option value="128">
				Announcements
			</option>
		</select><button class="button safe float-right update">Update</button>
	</div>
</div>
</div></td>
</tr><tr class="header2">
<td></td><td style="width:100%;"><div style="text-align:right;">
<form action="/script/Forums/SetOptions.aspx?floc=%2fArticles%2f838511%2fProcedural-seamless-noise-texture-generator&amp;fid=1872579&amp;df=90&amp;mpp=25&amp;sort=Position&amp;view=Normal&amp;spc=Relaxed&amp;prof=True" method="get" style="margin:0;padding:0;">
<input type="hidden" name="fid" value="1872579" /><input type="hidden" name="currentQS" value="floc=%2fArticles%2f838511%2fProcedural-seamless-noise-texture-generator&amp;fid=1872579&amp;df=90&amp;mpp=25&amp;sort=Position&amp;view=Normal&amp;spc=Relaxed&amp;prof=True" /><input type="hidden" name="floc" value="/Articles/838511/Procedural-seamless-noise-texture-generator" />Spacing<select size="1" class="dropdown" name="spc">
<option selected value="Relaxed">Relaxed</option><option value="Compact">Compact</option><option value="Tight">Tight</option>
</select>&nbsp;&nbsp;Layout<select size="1" class="dropdown" name="view">
<option selected value="Normal">Normal</option><option value="Topic">Open Topics</option><option value="Expanded">Open All</option><option value="Thread">Thread View</option>
</select>&nbsp;&nbsp;Per page<select size="1" class="dropdown" name="mpp">
<option value="10">10</option><option selected value="25">25</option><option value="50">50</option>
</select>&nbsp;&nbsp;&nbsp;<input type="submit" value="Update" name="SetOpt" class="button" />
</form>
</div></td>
</tr>
</table></td>
</tr><tr>
<td><a name="xx0xx"></a><table border="0" cellpadding="2px" cellspacing="0" width="100%">
<tr class="navbar">
<td></td><td style="text-align:right;width:50%;"></td><td style="text-align:right;white-space:nowrap;"><span class="nav-link disabled">First</span> <span class="nav-link disabled">Prev</span> <span class="nav-link disabled">Next</span></td>
</tr>
</table></td>
</tr><tr>
<td><table border="0" cellpadding="0" cellspacing="0" width="100%" class="fixed-layout blank-background">
<tr id="F5631133_h0" class="header hover-row root">
<td class="subject-line normal " width="100%"><table border="0" cellpadding="0" cellspacing="0" width="100%">
<tr>
<td width="20px" class="indent"><a name="xx5631133xx"></a><img height="16px" width="16px" align="top" src="https://codeproject.global.ssl.fastly.net/script/Forums/Images/msg_question.gif" alt="Question" title="Question" /></td><td class="subject hover-container"><a class="message-link" name="5631133" parent="0" thread="5631133" href="/Messages/5631133/Bitmap-output.aspx">Bitmap output</a> <a onclick="return forumControl286884.Pin(this);" rel="nofollow" href="#" title="Click to pin message"><img src="https://codeproject.global.ssl.fastly.net/script/Forums/Images/pin.png" border="0" align="top" alt="Pin" width="13px" height="13px" /></a><div class="mobile-only">
	<span class="author">Member 14461873</span><span class="date">24-May-19  7:06</span>
</div></td><td class="icon"><img border="0" src="https://codeproject.global.ssl.fastly.net/App_Themes/CodeProject/Img/icn-member-16.gif" title="member" alt="member" height="16px" /></td><td class="author"><span><a href="/script/Membership/View.aspx?mid=14461873">Member 14461873</a></span></td><td class="date"><span>24-May-19  7:06&nbsp;</span></td>
</tr>
</table></td>
</tr><tr id="F5631133_h1" class="content root selected" style="display:none;">
<td class="normal" width="100%"><table border="0" cellpadding="0" cellspacing="0" width="100%">
<tr valign="top">
<td class="indent align-right" style="width:20px;"><span class="voteform vertical" ownerid="14461873" msgid="5631133" votingType="GoodOrBad"></span></td><td class="text"><table border="0" cellpadding="0" cellspacing="5px" width="100%">
<tr>
<td><table border="0" cellpadding="0" cellspacing="0" width="100%">
<tr>
<td colspan="2">This is wonderful! Great work on this. Did you implement a way to output these bitmaps to jpeg or png?<br /></td>
</tr><tr class="footer" style="vertical-align:top;">
<td><a class="new-message" href="/script/Forums/Edit.aspx?fid=1872579&amp;select=5631133&amp;floc=/Articles/838511/Procedural-seamless-noise-texture-generator&amp;action=r" title="Reply">Reply</a>·<wbr><a class="nav-link" href="/script/Forums/Edit.aspx?fid=1872579&amp;select=5631133&amp;floc=/Articles/838511/Procedural-seamless-noise-texture-generator&amp;action=e" title="Send the author a private email">Email</a>·<wbr><a href="/Articles/838511/Procedural-seamless-noise-texture-generator?fid=1872579&amp;df=90&amp;mpp=25&amp;sort=Position&amp;spc=Relaxed&amp;tid=5631133" title="View&nbsp;Thread">View&nbsp;Thread</a></td><td style="text-align:right;"><span id="MVF5631133" data-ref="3_5631133" class="rating-label" name="RateItem_5631133"><span class="rating-result"><span></span></span></span><a href="/Messages/5631133/Bitmap-output.aspx" title="Get permanent link" style="border:0 !important"><img src="https://codeproject.global.ssl.fastly.net/App_Themes/CodeProject/Img/link32.png" style="width:14px;height:14px;border:0" /></a>&nbsp;<span data-type="bookmark" data-size="small" data-ref="3_5631133"></span>&nbsp;<span class="reportform" ownerid="14461873" msgid="5631133"></span></td>
</tr>
</table></td>
</tr>
</table></td>
</tr>
</table></td>
