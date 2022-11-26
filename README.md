<h1 id="purpose">Purpose</h1>
<p>Purpose of this work folder.</p>
<p>Ideally store a minimum working example data set in data folder.</p>
<p>Add binary files in bin, and closed R functions in code. Human
Readable settings files (e.g. csv) should be placed in settings/</p>
<pre><code>##          used (Mb) gc trigger (Mb) max used (Mb)
## Ncells 473017 25.3    1029354   55   644245 34.5
## Vcells 847677  6.5    8388608   64  1635428 12.5</code></pre>
<pre><code>## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.4.0      ✔ purrr   0.3.5 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
## ✔ readr   2.1.3      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()</code></pre>
<h1 id="question-1">Question 1</h1>
<p>NOTE TO SELF –&gt; FIGURE OUT HOW TO LET A FEW ACTIVE FUNDS REPRESENT
THE ENTIRE SAMPLE, IN ORDER TO COMPARE. IE, EW PORTF? PCA? CLUSTER?</p>
<div class="sourceCode" id="cb3"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>ASISA <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/ASISA.rds&quot;</span>) <span class="co"># ASISA Active Managers. Notice that there are 227 different actively managed funds.</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb3-3"><a href="#cb3-3" aria-hidden="true" tabindex="-1"></a>                                    <span class="co"># Monthly observations, 2002-11-30 to 2022-10-31</span></span>
<span id="cb3-4"><a href="#cb3-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb3-5"><a href="#cb3-5" aria-hidden="true" tabindex="-1"></a>BM <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/Capped_SWIX.rds&quot;</span>) <span class="co"># Benchmark: Capped Swix. Monthly observations, 1999-12-31 to 2022-10-31</span></span>
<span id="cb3-6"><a href="#cb3-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb3-7"><a href="#cb3-7" aria-hidden="true" tabindex="-1"></a>AI_Fund <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/AI_Max_Fund.rds&quot;</span>) <span class="co"># My Systematic AI Fund. Monthly observations, 2003-01-31 to 2022-10-31</span></span></code></pre></div>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a>df <span class="ot">&lt;-</span> <span class="fu">left_join</span>(BM <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Tickers&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Returns&quot;</span>) , ASISA <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Returns&quot;</span>), <span class="at">by=</span> <span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> <span class="fu">left_join</span>(AI_Fund <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Tickers&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Returns&quot;</span>), <span class="at">by =</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date ,<span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Returns&quot;</span>) </span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a>p <span class="ot">&lt;-</span> df <span class="sc">|&gt;</span> <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> <span class="fu">filter</span>(Name <span class="sc">==</span> <span class="fu">c</span>(<span class="st">&quot;Our_Fund&quot;</span>, <span class="st">&quot;J433&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-8"><a href="#cb4-8" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Returns, <span class="at">color =</span> Name), <span class="at">size =</span> <span class="fl">0.4</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span> </span>
<span id="cb4-9"><a href="#cb4-9" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-10"><a href="#cb4-10" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb4-11"><a href="#cb4-11" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">28</span>),</span>
<span id="cb4-12"><a href="#cb4-12" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb4-13"><a href="#cb4-13" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> F) <span class="sc">+</span> </span>
<span id="cb4-14"><a href="#cb4-14" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-15"><a href="#cb4-15" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb4-16"><a href="#cb4-16" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-17"><a href="#cb4-17" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;Cumulative Returns&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb4-18"><a href="#cb4-18" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Illustrating fmxdat Auxilliary functions for ggplot&quot;</span>,</span>
<span id="cb4-19"><a href="#cb4-19" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;If not subtitle, make blank and Subtitle size small to make a gap</span><span class="sc">\n</span><span class="st">between plot and Title. Test this yourself&quot;</span>)</span></code></pre></div>
<pre><code>## Warning in Name == c(&quot;Our_Fund&quot;, &quot;J433&quot;): longer object length is not a multiple
## of shorter object length</code></pre>
<pre><code>## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.</code></pre>
<div class="sourceCode" id="cb7"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot now adds finishing touches easily:</span></span>
<span id="cb7-2"><a href="#cb7-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-3"><a href="#cb7-3" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">finplot</span>(p, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>)</span></code></pre></div>
<pre><code>## Warning: Removed 19 rows containing missing values (`geom_line()`).</code></pre>
<p><img
src="README_files/figure-html/unnamed-chunk-2-1.png" /><!-- --></p>
<h1 id="question-2">Question 2</h1>
<div class="sourceCode" id="cb9"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a>SA_bonds <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/SA_Bonds.rds&quot;</span>)</span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a>BE_Infl <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/BE_Infl.rds&quot;</span>)</span>
<span id="cb9-3"><a href="#cb9-3" aria-hidden="true" tabindex="-1"></a>bonds_2y <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/bonds_2y.rds&quot;</span>)</span>
<span id="cb9-4"><a href="#cb9-4" aria-hidden="true" tabindex="-1"></a>bonds_10y <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/bonds_10y.rds&quot;</span>)</span>
<span id="cb9-5"><a href="#cb9-5" aria-hidden="true" tabindex="-1"></a>usdzar <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/usdzar.rds&quot;</span>)</span>
<span id="cb9-6"><a href="#cb9-6" aria-hidden="true" tabindex="-1"></a>ZA_Infl <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/ZA_Infl.rds&quot;</span>)</span>
<span id="cb9-7"><a href="#cb9-7" aria-hidden="true" tabindex="-1"></a>IV <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/IV.rds&quot;</span>)</span></code></pre></div>
<div class="sourceCode" id="cb10"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb10-1"><a href="#cb10-1" aria-hidden="true" tabindex="-1"></a>Countries_to_compare <span class="ot">&lt;-</span> <span class="fu">c</span>(<span class="st">&quot;Germany&quot;</span>, <span class="st">&quot;ZA&quot;</span>, <span class="st">&quot;US&quot;</span>, <span class="st">&quot;CHINA&quot;</span>, <span class="st">&quot;Japan&quot;</span>, <span class="st">&quot;Brazil&quot;</span>)</span>
<span id="cb10-2"><a href="#cb10-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-3"><a href="#cb10-3" aria-hidden="true" tabindex="-1"></a>bonds_10y_adj <span class="ot">&lt;-</span> bonds_10y <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Bond_10Yr&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb10-4"><a href="#cb10-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-5"><a href="#cb10-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">left_join</span>(SA_bonds <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="fu">c</span>(date, ZA_10Yr)), <span class="at">by=</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb10-6"><a href="#cb10-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-7"><a href="#cb10-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Bond_10Yr&quot;</span>)<span class="sc">|&gt;</span> </span>
<span id="cb10-8"><a href="#cb10-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-9"><a href="#cb10-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Name =</span> <span class="fu">gsub</span>(<span class="st">&quot;_10Yr&quot;</span>, <span class="st">&quot;&quot;</span>, Name)) <span class="sc">|&gt;</span> </span>
<span id="cb10-10"><a href="#cb10-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-11"><a href="#cb10-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>((Name <span class="sc">%in%</span> Countries_to_compare))  <span class="co"># Only filter the desired countries</span></span>
<span id="cb10-12"><a href="#cb10-12" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-13"><a href="#cb10-13" aria-hidden="true" tabindex="-1"></a><span class="co"># Upon initial attempt to format bonds_2y, I realised there is a duplicate entry, identified by the following </span></span>
<span id="cb10-14"><a href="#cb10-14" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-15"><a href="#cb10-15" aria-hidden="true" tabindex="-1"></a>duplicate_entries_identification <span class="ot">&lt;-</span> bonds_2y <span class="sc">%&gt;%</span></span>
<span id="cb10-16"><a href="#cb10-16" aria-hidden="true" tabindex="-1"></a>                                    dplyr<span class="sc">::</span><span class="fu">group_by</span>(date, Name) <span class="sc">%&gt;%</span></span>
<span id="cb10-17"><a href="#cb10-17" aria-hidden="true" tabindex="-1"></a>                                    dplyr<span class="sc">::</span><span class="fu">summarise</span>(<span class="at">n =</span> dplyr<span class="sc">::</span><span class="fu">n</span>(), <span class="at">.groups =</span> <span class="st">&quot;drop&quot;</span>) <span class="sc">%&gt;%</span></span>
<span id="cb10-18"><a href="#cb10-18" aria-hidden="true" tabindex="-1"></a>                                    dplyr<span class="sc">::</span><span class="fu">filter</span>(n <span class="sc">&gt;</span> 1L)</span>
<span id="cb10-19"><a href="#cb10-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-20"><a href="#cb10-20" aria-hidden="true" tabindex="-1"></a><span class="co"># Since the duplicate entry is &quot;Chile_2yr&quot;, I remove Chile in order to pivot_wider. </span></span>
<span id="cb10-21"><a href="#cb10-21" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-22"><a href="#cb10-22" aria-hidden="true" tabindex="-1"></a>bonds_2y_adj <span class="ot">&lt;-</span> bonds_2y <span class="sc">|&gt;</span> <span class="fu">filter</span>(<span class="sc">!</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;Chile_2yr&quot;</span>))) <span class="sc">|&gt;</span>  <span class="co"># remove chile</span></span>
<span id="cb10-23"><a href="#cb10-23" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-24"><a href="#cb10-24" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Bond_2Yr&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb10-25"><a href="#cb10-25" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-26"><a href="#cb10-26" aria-hidden="true" tabindex="-1"></a>    <span class="fu">left_join</span>(SA_bonds <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="fu">c</span>(date, ZA_2Yr)), <span class="at">by=</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb10-27"><a href="#cb10-27" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-28"><a href="#cb10-28" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Bond_2Yr&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb10-29"><a href="#cb10-29" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-30"><a href="#cb10-30" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Name =</span> <span class="fu">gsub</span>(<span class="st">&quot;_2yr&quot;</span>, <span class="st">&quot;&quot;</span>, Name)) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="at">Name =</span> <span class="fu">gsub</span>(<span class="st">&quot;_2Yr&quot;</span>, <span class="st">&quot;&quot;</span>, Name)) <span class="sc">|&gt;</span> </span>
<span id="cb10-31"><a href="#cb10-31" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-32"><a href="#cb10-32" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(Name <span class="sc">%in%</span> Countries_to_compare) </span>
<span id="cb10-33"><a href="#cb10-33" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-34"><a href="#cb10-34" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I proceed to merge the 2Yr and 10Yr global yields and their spreads in one tbl</span></span>
<span id="cb10-35"><a href="#cb10-35" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb10-36"><a href="#cb10-36" aria-hidden="true" tabindex="-1"></a>Global_bonds_data <span class="ot">&lt;-</span> <span class="fu">inner_join</span>(bonds_2y_adj, bonds_10y_adj, <span class="at">by=</span> <span class="fu">c</span>(<span class="st">&quot;date&quot;</span>, <span class="st">&quot;Name&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb10-37"><a href="#cb10-37" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb10-38"><a href="#cb10-38" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Spread =</span> Bond_10Yr <span class="sc">-</span> Bond_2Yr)</span></code></pre></div>
<div class="sourceCode" id="cb11"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb11-1"><a href="#cb11-1" aria-hidden="true" tabindex="-1"></a>Global_bonds_plot <span class="ot">&lt;-</span>    Global_bonds_data <span class="sc">|&gt;</span> <span class="fu">select</span>(date,Name ,Spread) <span class="sc">|&gt;</span> </span>
<span id="cb11-2"><a href="#cb11-2" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb11-3"><a href="#cb11-3" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb11-4"><a href="#cb11-4" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb11-5"><a href="#cb11-5" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Spread , <span class="at">color =</span> Name), <span class="at">size =</span> <span class="fl">0.8</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb11-6"><a href="#cb11-6" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb11-7"><a href="#cb11-7" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb11-8"><a href="#cb11-8" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb11-9"><a href="#cb11-9" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb11-10"><a href="#cb11-10" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb11-11"><a href="#cb11-11" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb11-12"><a href="#cb11-12" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb11-13"><a href="#cb11-13" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb11-14"><a href="#cb11-14" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;Yield Spread (%)&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb11-15"><a href="#cb11-15" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Global Bond Market Yield Spreads&quot;</span>,</span>
<span id="cb11-16"><a href="#cb11-16" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb11-17"><a href="#cb11-17" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb11-18"><a href="#cb11-18" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb11-19"><a href="#cb11-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb11-20"><a href="#cb11-20" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(Global_bonds_plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> F)</span></code></pre></div>
<pre><code>## Warning: Removed 13276 rows containing missing values (`geom_line()`).</code></pre>
<p><img
src="README_files/figure-html/Global%20Bond%20Yield%20Spreads%20Plot-1.png" /><!-- --></p>
<div class="sourceCode" id="cb13"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb13-1"><a href="#cb13-1" aria-hidden="true" tabindex="-1"></a>SA_Bonds_Plot <span class="ot">&lt;-</span> Global_bonds_data <span class="sc">|&gt;</span> <span class="fu">filter</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;ZA&quot;</span>)) <span class="sc">|&gt;</span> <span class="fu">left_join</span>(usdzar <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="sc">-</span>Name), <span class="at">by =</span> <span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb13-2"><a href="#cb13-2" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb13-3"><a href="#cb13-3" aria-hidden="true" tabindex="-1"></a>    <span class="fu">rename</span>( <span class="at">R_USD =</span> Price) <span class="sc">|&gt;</span> <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span><span class="fu">c</span>(date, Name), <span class="at">names_to =</span> <span class="st">&quot;Description&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Values&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb13-4"><a href="#cb13-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb13-5"><a href="#cb13-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb13-6"><a href="#cb13-6" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb13-7"><a href="#cb13-7" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Values , <span class="at">color =</span> Description), <span class="at">size =</span> <span class="fl">0.8</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb13-8"><a href="#cb13-8" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb13-9"><a href="#cb13-9" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb13-10"><a href="#cb13-10" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb13-11"><a href="#cb13-11" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb13-12"><a href="#cb13-12" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb13-13"><a href="#cb13-13" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb13-14"><a href="#cb13-14" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb13-15"><a href="#cb13-15" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb13-16"><a href="#cb13-16" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb13-17"><a href="#cb13-17" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;SA Bond Yields, Spread, and R/USD Exchange Rate&quot;</span>,</span>
<span id="cb13-18"><a href="#cb13-18" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb13-19"><a href="#cb13-19" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb13-20"><a href="#cb13-20" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb13-21"><a href="#cb13-21" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb13-22"><a href="#cb13-22" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(SA_Bonds_Plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> F)</span></code></pre></div>
<pre><code>## Warning: Removed 7770 rows containing missing values (`geom_line()`).</code></pre>
<p><img
src="README_files/figure-html/US%20and%20SA%20Real%20Yield%20Spread-1.png" /><!-- --></p>
<div class="sourceCode" id="cb15"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb15-1"><a href="#cb15-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Partition ZA and US yield spread data into post and pre GFC and convert to xts</span></span>
<span id="cb15-2"><a href="#cb15-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb15-3"><a href="#cb15-3" aria-hidden="true" tabindex="-1"></a>pre_GFC_xts <span class="ot">&lt;-</span> Global_bonds_data<span class="sc">|&gt;</span> <span class="fu">filter</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;ZA&quot;</span>, <span class="st">&quot;US&quot;</span>)) <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Name, Spread) <span class="sc">|&gt;</span> </span>
<span id="cb15-4"><a href="#cb15-4" aria-hidden="true" tabindex="-1"></a>     </span>
<span id="cb15-5"><a href="#cb15-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&lt;=</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="dv">20081031</span>)) <span class="sc">|&gt;</span>  </span>
<span id="cb15-6"><a href="#cb15-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb15-7"><a href="#cb15-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;=</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="dv">19991206</span>)) <span class="sc">|&gt;</span>  <span class="co"># Start from ZA&#39;s first observation</span></span>
<span id="cb15-8"><a href="#cb15-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb15-9"><a href="#cb15-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Spread, <span class="at">spread_by =</span> Name) </span>
<span id="cb15-10"><a href="#cb15-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb15-11"><a href="#cb15-11" aria-hidden="true" tabindex="-1"></a>post_GFC_xts <span class="ot">&lt;-</span> Global_bonds_data<span class="sc">|&gt;</span> <span class="fu">filter</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;ZA&quot;</span>, <span class="st">&quot;US&quot;</span>)) <span class="sc">|&gt;</span> <span class="fu">select</span>(date,Name ,Spread) <span class="sc">|&gt;</span> </span>
<span id="cb15-12"><a href="#cb15-12" aria-hidden="true" tabindex="-1"></a>     </span>
<span id="cb15-13"><a href="#cb15-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;=</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="dv">20081031</span>)) <span class="sc">|&gt;</span> <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Spread, <span class="at">spread_by =</span> Name) </span>
<span id="cb15-14"><a href="#cb15-14" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb15-15"><a href="#cb15-15" aria-hidden="true" tabindex="-1"></a><span class="co"># Use performance analytics package for statistical table</span></span>
<span id="cb15-16"><a href="#cb15-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb15-17"><a href="#cb15-17" aria-hidden="true" tabindex="-1"></a>table_pre_GFC <span class="ot">&lt;-</span> PerformanceAnalytics<span class="sc">::</span><span class="fu">table.Stats</span>(pre_GFC_xts, <span class="at">ci=</span><span class="fl">0.95</span>, <span class="at">digits =</span> <span class="dv">2</span>)</span></code></pre></div>
<pre><code>## Warning in log(1 + x): NaNs produced</code></pre>
<div class="sourceCode" id="cb17"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb17-1"><a href="#cb17-1" aria-hidden="true" tabindex="-1"></a>table_post_GFC <span class="ot">&lt;-</span> PerformanceAnalytics<span class="sc">::</span><span class="fu">table.Stats</span>(post_GFC_xts, <span class="at">ci=</span><span class="fl">0.95</span>, <span class="at">digits =</span> <span class="dv">2</span>)</span>
<span id="cb17-2"><a href="#cb17-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-3"><a href="#cb17-3" aria-hidden="true" tabindex="-1"></a><span class="co"># Only select the desired stats</span></span>
<span id="cb17-4"><a href="#cb17-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-5"><a href="#cb17-5" aria-hidden="true" tabindex="-1"></a>table_pre_GFC <span class="ot">&lt;-</span> table_pre_GFC[<span class="fu">c</span>(<span class="dv">3</span>,<span class="dv">6</span>,<span class="dv">9</span>,<span class="dv">10</span>,<span class="dv">11</span>,<span class="dv">12</span>,<span class="dv">14</span>,<span class="dv">15</span>,<span class="dv">16</span>),] </span>
<span id="cb17-6"><a href="#cb17-6" aria-hidden="true" tabindex="-1"></a>table_post_GFC <span class="ot">&lt;-</span> table_post_GFC[<span class="fu">c</span>(<span class="dv">3</span>,<span class="dv">6</span>,<span class="dv">9</span>,<span class="dv">10</span>,<span class="dv">11</span>,<span class="dv">12</span>,<span class="dv">14</span>,<span class="dv">15</span>,<span class="dv">16</span>),] </span>
<span id="cb17-7"><a href="#cb17-7" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-8"><a href="#cb17-8" aria-hidden="true" tabindex="-1"></a><span class="co"># Finally summarise neatly in a table using kable</span></span>
<span id="cb17-9"><a href="#cb17-9" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-10"><a href="#cb17-10" aria-hidden="true" tabindex="-1"></a>final_stats_table <span class="ot">&lt;-</span> </span>
<span id="cb17-11"><a href="#cb17-11" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb17-12"><a href="#cb17-12" aria-hidden="true" tabindex="-1"></a>    table_pre_GFC <span class="sc">|&gt;</span>  <span class="fu">data.frame</span>() <span class="sc">|&gt;</span>  tibble<span class="sc">::</span><span class="fu">rownames_to_column</span>()<span class="sc">|&gt;</span> </span>
<span id="cb17-13"><a href="#cb17-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">left_join</span>(table_post_GFC<span class="sc">|&gt;</span>  <span class="fu">data.frame</span>() <span class="sc">|&gt;</span>  tibble<span class="sc">::</span><span class="fu">rownames_to_column</span>(), <span class="at">by =</span> <span class="st">&quot;rowname&quot;</span> ) <span class="sc">|&gt;</span> </span>
<span id="cb17-14"><a href="#cb17-14" aria-hidden="true" tabindex="-1"></a>    <span class="fu">rename</span>(<span class="at">Description =</span> rowname) <span class="sc">|&gt;</span> </span>
<span id="cb17-15"><a href="#cb17-15" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb17-16"><a href="#cb17-16" aria-hidden="true" tabindex="-1"></a>    knitr<span class="sc">::</span><span class="fu">kable</span>(<span class="at">col.names =</span> <span class="fu">c</span>(<span class="st">&quot;Description&quot;</span>,</span>
<span id="cb17-17"><a href="#cb17-17" aria-hidden="true" tabindex="-1"></a>                           <span class="st">&quot;SA&quot;</span>,</span>
<span id="cb17-18"><a href="#cb17-18" aria-hidden="true" tabindex="-1"></a>                           <span class="st">&quot;US&quot;</span>,</span>
<span id="cb17-19"><a href="#cb17-19" aria-hidden="true" tabindex="-1"></a>                           <span class="st">&quot;SA&quot;</span>, <span class="st">&quot;US&quot;</span>)) <span class="sc">|&gt;</span> <span class="fu">kable_classic</span>(<span class="at">full_width =</span> F) <span class="sc">|&gt;</span> </span>
<span id="cb17-20"><a href="#cb17-20" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb17-21"><a href="#cb17-21" aria-hidden="true" tabindex="-1"></a>    <span class="fu">add_header_above</span>(<span class="fu">c</span>(<span class="st">&quot; &quot;</span> <span class="ot">=</span> <span class="dv">1</span>, <span class="st">&quot;Pre GFC&quot;</span> <span class="ot">=</span> <span class="dv">2</span>, <span class="st">&quot;Post GFC&quot;</span> <span class="ot">=</span> <span class="dv">2</span>))</span>
<span id="cb17-22"><a href="#cb17-22" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-23"><a href="#cb17-23" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-24"><a href="#cb17-24" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb17-25"><a href="#cb17-25" aria-hidden="true" tabindex="-1"></a>final_stats_table</span></code></pre></div>
<table class=" lightable-classic" style="font-family: &quot;Arial Narrow&quot;, &quot;Source Sans Pro&quot;, sans-serif; width: auto !important; margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="empty-cells: hide;" colspan="1">
</th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">
<div style="border-bottom: 1px solid #111111; margin-bottom: -1px; ">
Pre GFC
</div>
</th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">
<div style="border-bottom: 1px solid #111111; margin-bottom: -1px; ">
Post GFC
</div>
</th>
</tr>
<tr>
<th style="text-align:left;">
Description
</th>
<th style="text-align:right;">
SA
</th>
<th style="text-align:right;">
US
</th>
<th style="text-align:right;">
SA
</th>
<th style="text-align:right;">
US
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Minimum
</td>
<td style="text-align:right;">
-0.51
</td>
<td style="text-align:right;">
-1.80
</td>
<td style="text-align:right;">
-0.05
</td>
<td style="text-align:right;">
-0.58
</td>
</tr>
<tr>
<td style="text-align:left;">
Arithmetic Mean
</td>
<td style="text-align:right;">
1.04
</td>
<td style="text-align:right;">
0.50
</td>
<td style="text-align:right;">
1.43
</td>
<td style="text-align:right;">
1.97
</td>
</tr>
<tr>
<td style="text-align:left;">
Maximum
</td>
<td style="text-align:right;">
2.74
</td>
<td style="text-align:right;">
2.85
</td>
<td style="text-align:right;">
2.91
</td>
<td style="text-align:right;">
6.30
</td>
</tr>
<tr>
<td style="text-align:left;">
SE Mean
</td>
<td style="text-align:right;">
0.02
</td>
<td style="text-align:right;">
0.02
</td>
<td style="text-align:right;">
0.01
</td>
<td style="text-align:right;">
0.02
</td>
</tr>
<tr>
<td style="text-align:left;">
LCL Mean (0.95)
</td>
<td style="text-align:right;">
1.00
</td>
<td style="text-align:right;">
0.46
</td>
<td style="text-align:right;">
1.40
</td>
<td style="text-align:right;">
1.93
</td>
</tr>
<tr>
<td style="text-align:left;">
UCL Mean (0.95)
</td>
<td style="text-align:right;">
1.08
</td>
<td style="text-align:right;">
0.55
</td>
<td style="text-align:right;">
1.45
</td>
<td style="text-align:right;">
2.00
</td>
</tr>
<tr>
<td style="text-align:left;">
Stdev
</td>
<td style="text-align:right;">
0.96
</td>
<td style="text-align:right;">
1.10
</td>
<td style="text-align:right;">
0.80
</td>
<td style="text-align:right;">
0.99
</td>
</tr>
<tr>
<td style="text-align:left;">
Skewness
</td>
<td style="text-align:right;">
0.04
</td>
<td style="text-align:right;">
-0.14
</td>
<td style="text-align:right;">
0.03
</td>
<td style="text-align:right;">
1.15
</td>
</tr>
<tr>
<td style="text-align:left;">
Kurtosis
</td>
<td style="text-align:right;">
-1.52
</td>
<td style="text-align:right;">
-0.92
</td>
<td style="text-align:right;">
-1.10
</td>
<td style="text-align:right;">
1.39
</td>
</tr>
</tbody>
</table>
<div class="sourceCode" id="cb18"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb18-1"><a href="#cb18-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Break-even inflation is the difference between the nominal yield on a fixed-rate investment and the real yield (fixed spread) on an inflation-linked investment of similar maturity and credit quality. If inflation averages more than the break-even, the inflation-linked investment will outperform the fixed-rate.</span></span>
<span id="cb18-2"><a href="#cb18-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb18-3"><a href="#cb18-3" aria-hidden="true" tabindex="-1"></a><span class="co"># Find the monthly values of BE Infl Yiels to compare to monthly inflation data </span></span>
<span id="cb18-4"><a href="#cb18-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb18-5"><a href="#cb18-5" aria-hidden="true" tabindex="-1"></a>BE_Infl_adj <span class="ot">&lt;-</span> BE_Infl <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="at">YearMonth =</span> <span class="fu">format</span>(date, <span class="st">&quot;%Y-%m&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb18-6"><a href="#cb18-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-7"><a href="#cb18-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(YearMonth) <span class="sc">|&gt;</span> <span class="fu">filter</span>(date <span class="sc">==</span> <span class="fu">last</span>(date)) <span class="sc">|&gt;</span> </span>
<span id="cb18-8"><a href="#cb18-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-9"><a href="#cb18-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>() <span class="sc">|&gt;</span> <span class="fu">rename</span>(<span class="at">BEI =</span> Price)<span class="sc">|&gt;</span> <span class="fu">select</span>(YearMonth, BEI)</span>
<span id="cb18-10"><a href="#cb18-10" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb18-11"><a href="#cb18-11" aria-hidden="true" tabindex="-1"></a><span class="co"># Notice that the number of days withing the BE inflation set is incorrect, so I rather use YearMonth as common column, </span></span>
<span id="cb18-12"><a href="#cb18-12" aria-hidden="true" tabindex="-1"></a><span class="co"># And then utilise the dateconverter command. </span></span>
<span id="cb18-13"><a href="#cb18-13" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb18-14"><a href="#cb18-14" aria-hidden="true" tabindex="-1"></a>BEI_infl_plot <span class="ot">&lt;-</span> ZA_Infl <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="at">YearMonth =</span> <span class="fu">format</span>(date, <span class="st">&quot;%Y-%m&quot;</span>) ) <span class="sc">|&gt;</span> </span>
<span id="cb18-15"><a href="#cb18-15" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-16"><a href="#cb18-16" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(YearMonth, Price) <span class="sc">|&gt;</span> <span class="fu">rename</span>(<span class="at">Inflation =</span> Price) <span class="sc">|&gt;</span> </span>
<span id="cb18-17"><a href="#cb18-17" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-18"><a href="#cb18-18" aria-hidden="true" tabindex="-1"></a>    <span class="fu">right_join</span>(BE_Infl_adj, <span class="at">by =</span> <span class="st">&quot;YearMonth&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb18-19"><a href="#cb18-19" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-20"><a href="#cb18-20" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">date =</span> <span class="fu">dateconverter</span>(<span class="fu">as.Date</span>(<span class="st">&quot;2012-05-01&quot;</span>), <span class="fu">as.Date</span>(<span class="st">&quot;2021-10-29&quot;</span>), <span class="st">&quot;calendarEOM&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb18-21"><a href="#cb18-21" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-22"><a href="#cb18-22" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(<span class="sc">-</span>YearMonth) <span class="sc">|&gt;</span> </span>
<span id="cb18-23"><a href="#cb18-23" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-24"><a href="#cb18-24" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Values&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb18-25"><a href="#cb18-25" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-26"><a href="#cb18-26" aria-hidden="true" tabindex="-1"></a>     <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb18-27"><a href="#cb18-27" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb18-28"><a href="#cb18-28" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Values , <span class="at">color =</span> Name), <span class="at">size =</span> <span class="fl">0.8</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb18-29"><a href="#cb18-29" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb18-30"><a href="#cb18-30" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb18-31"><a href="#cb18-31" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb18-32"><a href="#cb18-32" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb18-33"><a href="#cb18-33" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb18-34"><a href="#cb18-34" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb18-35"><a href="#cb18-35" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb18-36"><a href="#cb18-36" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb18-37"><a href="#cb18-37" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb18-38"><a href="#cb18-38" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;SA Break-Even Inflation Yield Versus Average Inflation Rate&quot;</span>,</span>
<span id="cb18-39"><a href="#cb18-39" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb18-40"><a href="#cb18-40" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb18-41"><a href="#cb18-41" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb18-42"><a href="#cb18-42" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb18-43"><a href="#cb18-43" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(BEI_infl_plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> F)</span></code></pre></div>
<p><img
src="README_files/figure-html/SA%20Break-Even%20Inflation%20Yield-1.png" /><!-- --></p>
<h1 id="question-3">Question 3</h1>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb19-1"><a href="#cb19-1" aria-hidden="true" tabindex="-1"></a>T40 <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/T40.rds&quot;</span>) <span class="co"># There are 92 stocks in this tbl</span></span>
<span id="cb19-2"><a href="#cb19-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb19-3"><a href="#cb19-3" aria-hidden="true" tabindex="-1"></a>RebDays <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/Rebalance_days.rds&quot;</span>)</span>
<span id="cb19-4"><a href="#cb19-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb19-5"><a href="#cb19-5" aria-hidden="true" tabindex="-1"></a>Capped_SWIX <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/Capped_SWIX.rds&quot;</span>) <span class="co"># This is the Monthly Capped and Weighted Portf Returns for SWIX Index (J433)</span></span>
<span id="cb19-6"><a href="#cb19-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb19-7"><a href="#cb19-7" aria-hidden="true" tabindex="-1"></a><span class="co"># I first shrink the dataframe to include only what in needed</span></span>
<span id="cb19-8"><a href="#cb19-8" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb19-9"><a href="#cb19-9" aria-hidden="true" tabindex="-1"></a>T40_a <span class="ot">&lt;-</span> T40 <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="sc">-</span>Short.Name) <span class="sc">|&gt;</span> </span>
<span id="cb19-10"><a href="#cb19-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb19-11"><a href="#cb19-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Tickers =</span> <span class="fu">gsub</span>(<span class="st">&quot; SJ Equity&quot;</span>, <span class="st">&quot;&quot;</span>, Tickers))  <span class="co"># Remove clutter in Tickers names</span></span></code></pre></div>
<div class="sourceCode" id="cb20"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb20-1"><a href="#cb20-1" aria-hidden="true" tabindex="-1"></a><span class="co"># I generate a tbl calculating both Indexes weighted returns by hand</span></span>
<span id="cb20-2"><a href="#cb20-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb20-3"><a href="#cb20-3" aria-hidden="true" tabindex="-1"></a>df_Port_ret <span class="ot">&lt;-</span> T40_a <span class="sc">|&gt;</span> </span>
<span id="cb20-4"><a href="#cb20-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-5"><a href="#cb20-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">J400 =</span> <span class="fu">coalesce</span>(J400, <span class="dv">0</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb20-6"><a href="#cb20-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-7"><a href="#cb20-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">J400 =</span> <span class="fu">coalesce</span>(J400, <span class="dv">0</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb20-8"><a href="#cb20-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-9"><a href="#cb20-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wret =</span> Return<span class="sc">*</span>J200) <span class="sc">|&gt;</span> </span>
<span id="cb20-10"><a href="#cb20-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-11"><a href="#cb20-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wret =</span> Return<span class="sc">*</span>J400) <span class="sc">|&gt;</span> </span>
<span id="cb20-12"><a href="#cb20-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-13"><a href="#cb20-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb20-14"><a href="#cb20-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-15"><a href="#cb20-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb20-16"><a href="#cb20-16" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-17"><a href="#cb20-17" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_pret =</span> <span class="fu">sum</span>(ALSI_wret)) <span class="sc">|&gt;</span> </span>
<span id="cb20-18"><a href="#cb20-18" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb20-19"><a href="#cb20-19" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_pret =</span> <span class="fu">sum</span>(SWIX_wret)) </span></code></pre></div>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb21-1"><a href="#cb21-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Lets calculate the weighted portfolio daily return for ALSI and SWIX using Safe_Returns to verify my by hand calculation</span></span>
<span id="cb21-2"><a href="#cb21-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-3"><a href="#cb21-3" aria-hidden="true" tabindex="-1"></a>Wghts_ALSI_xts <span class="ot">&lt;-</span> T40_a <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Tickers , J200) <span class="sc">|&gt;</span> <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> J200, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb21-4"><a href="#cb21-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-5"><a href="#cb21-5" aria-hidden="true" tabindex="-1"></a>Wghts_SWIX_xts <span class="ot">&lt;-</span> T40_a <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Tickers , J400) <span class="sc">|&gt;</span> <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> J400, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb21-6"><a href="#cb21-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-7"><a href="#cb21-7" aria-hidden="true" tabindex="-1"></a>Returns_xts <span class="ot">&lt;-</span> T40_a <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Tickers , Return) <span class="sc">|&gt;</span> <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Return, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb21-8"><a href="#cb21-8" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-9"><a href="#cb21-9" aria-hidden="true" tabindex="-1"></a><span class="co"># Set NA&#39;s to null to use PA&#39;s Safe_returns.Portfolio command</span></span>
<span id="cb21-10"><a href="#cb21-10" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-11"><a href="#cb21-11" aria-hidden="true" tabindex="-1"></a>Wghts_ALSI_xts[<span class="fu">is.na</span>(Wghts_ALSI_xts)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb21-12"><a href="#cb21-12" aria-hidden="true" tabindex="-1"></a>Wghts_SWIX_xts[<span class="fu">is.na</span>(Wghts_SWIX_xts)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb21-13"><a href="#cb21-13" aria-hidden="true" tabindex="-1"></a>Returns_xts[<span class="fu">is.na</span>(Returns_xts)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb21-14"><a href="#cb21-14" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-15"><a href="#cb21-15" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I calculate the weighed (uncapped) portfolio returns</span></span>
<span id="cb21-16"><a href="#cb21-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-17"><a href="#cb21-17" aria-hidden="true" tabindex="-1"></a>Port_Ret_ALSI <span class="ot">&lt;-</span> rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(<span class="at">R =</span> Returns_xts, <span class="at">weights =</span> Wghts_ALSI_xts, <span class="at">lag_weights =</span> T) <span class="sc">|&gt;</span> </span>
<span id="cb21-18"><a href="#cb21-18" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb21-19"><a href="#cb21-19" aria-hidden="true" tabindex="-1"></a>                 <span class="fu">xts_tbl</span>() <span class="sc">|&gt;</span> <span class="fu">rename</span>(<span class="at">ALSI_Ret =</span> portfolio.returns)</span>
<span id="cb21-20"><a href="#cb21-20" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-21"><a href="#cb21-21" aria-hidden="true" tabindex="-1"></a>Port_Ret_SWIX <span class="ot">&lt;-</span> rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(<span class="at">R =</span> Returns_xts, <span class="at">weights =</span> Wghts_SWIX_xts, <span class="at">lag_weights =</span> T) <span class="sc">|&gt;</span> </span>
<span id="cb21-22"><a href="#cb21-22" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb21-23"><a href="#cb21-23" aria-hidden="true" tabindex="-1"></a>                 <span class="fu">xts_tbl</span>() <span class="sc">|&gt;</span> <span class="fu">rename</span>(<span class="at">SWIX_Ret =</span> portfolio.returns)</span>
<span id="cb21-24"><a href="#cb21-24" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-25"><a href="#cb21-25" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I combine the above two weighted portfolio returns</span></span>
<span id="cb21-26"><a href="#cb21-26" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-27"><a href="#cb21-27" aria-hidden="true" tabindex="-1"></a>Merged_Port_Ret <span class="ot">&lt;-</span> <span class="fu">inner_join</span>(Port_Ret_ALSI, Port_Ret_SWIX, <span class="at">by=</span> <span class="st">&quot;date&quot;</span>)</span>
<span id="cb21-28"><a href="#cb21-28" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb21-29"><a href="#cb21-29" aria-hidden="true" tabindex="-1"></a><span class="co"># I verify that my by hand calc and the Safe_return calc is the same:</span></span>
<span id="cb21-30"><a href="#cb21-30" aria-hidden="true" tabindex="-1"></a><span class="co"># df_Port_ret |&gt; select(date, ALSI_pret, SWIX_pret) |&gt; group_by(date, ALSI_pret, SWIX_pret ) |&gt; summarise()</span></span>
<span id="cb21-31"><a href="#cb21-31" aria-hidden="true" tabindex="-1"></a><span class="co"># Happy ---&gt; They are the same</span></span></code></pre></div>
<div class="sourceCode" id="cb22"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb22-1"><a href="#cb22-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I proceed to calculate the Portfolios&#39; cumulative return and plot it</span></span>
<span id="cb22-2"><a href="#cb22-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb22-3"><a href="#cb22-3" aria-hidden="true" tabindex="-1"></a>Cum_ret <span class="ot">&lt;-</span> Merged_Port_Ret <span class="sc">|&gt;</span> <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb22-4"><a href="#cb22-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb22-5"><a href="#cb22-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="fu">across</span>(<span class="at">.cols =</span> <span class="sc">-</span>date, <span class="at">.fns =</span> <span class="sc">~</span><span class="fu">cumprod</span>(<span class="dv">1</span> <span class="sc">+</span> .))) <span class="sc">|&gt;</span> </span>
<span id="cb22-6"><a href="#cb22-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb22-7"><a href="#cb22-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="fu">across</span>(<span class="at">.cols =</span> <span class="sc">-</span>date, <span class="at">.fns =</span> <span class="sc">~</span>.<span class="sc">/</span><span class="fu">first</span>(.))) <span class="sc">|&gt;</span> <span class="co"># Start at 1</span></span>
<span id="cb22-8"><a href="#cb22-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb22-9"><a href="#cb22-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">rename</span>(<span class="at">ALSI =</span> ALSI_Ret, <span class="at">SWIX=</span> SWIX_Ret) <span class="sc">|&gt;</span> </span>
<span id="cb22-10"><a href="#cb22-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb22-11"><a href="#cb22-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols=</span><span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Index&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Cumret&quot;</span>)</span>
<span id="cb22-12"><a href="#cb22-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb22-13"><a href="#cb22-13" aria-hidden="true" tabindex="-1"></a><span class="co"># And finally the plot</span></span>
<span id="cb22-14"><a href="#cb22-14" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb22-15"><a href="#cb22-15" aria-hidden="true" tabindex="-1"></a>Indexes_Cum_ret_plot <span class="ot">&lt;-</span>    Cum_ret <span class="sc">|&gt;</span> </span>
<span id="cb22-16"><a href="#cb22-16" aria-hidden="true" tabindex="-1"></a>       </span>
<span id="cb22-17"><a href="#cb22-17" aria-hidden="true" tabindex="-1"></a>       <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb22-18"><a href="#cb22-18" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb22-19"><a href="#cb22-19" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Cumret , <span class="at">color =</span> Index), <span class="at">size =</span> <span class="fl">0.6</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb22-20"><a href="#cb22-20" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb22-21"><a href="#cb22-21" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb22-22"><a href="#cb22-22" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb22-23"><a href="#cb22-23" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb22-24"><a href="#cb22-24" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb22-25"><a href="#cb22-25" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb22-26"><a href="#cb22-26" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb22-27"><a href="#cb22-27" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb22-28"><a href="#cb22-28" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb22-29"><a href="#cb22-29" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Cumulative Returns of ALSI and SWIX Indexes&quot;</span>,</span>
<span id="cb22-30"><a href="#cb22-30" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb22-31"><a href="#cb22-31" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb22-32"><a href="#cb22-32" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb22-33"><a href="#cb22-33" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb22-34"><a href="#cb22-34" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(Indexes_Cum_ret_plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;1 year&quot;</span>, <span class="at">darkcol =</span> T)</span></code></pre></div>
<pre><code>## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.</code></pre>
<p><img
src="README_files/figure-html/Portfolios%20cumulative%20returns-1.png" /><!-- --></p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb24-1"><a href="#cb24-1" aria-hidden="true" tabindex="-1"></a><span class="co"># I now create the final tbl that includes the weighted return contribition of each sector  to the overall weighted portfolio return (daily) </span></span>
<span id="cb24-2"><a href="#cb24-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-3"><a href="#cb24-3" aria-hidden="true" tabindex="-1"></a>df_Port_ret_final <span class="ot">&lt;-</span> df_Port_ret <span class="sc">|&gt;</span> <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb24-4"><a href="#cb24-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-5"><a href="#cb24-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date, Sector) <span class="sc">|&gt;</span>       <span class="co"># Group by Market Cap to calc each category&#39;s contr to wieghted portf return</span></span>
<span id="cb24-6"><a href="#cb24-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-7"><a href="#cb24-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wght_sector =</span> <span class="fu">coalesce</span>(J400, <span class="dv">0</span>)) <span class="sc">|&gt;</span> <span class="co"># Make NA&#39;s 0 to use PA later</span></span>
<span id="cb24-8"><a href="#cb24-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-9"><a href="#cb24-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wght_sector =</span> <span class="fu">coalesce</span>(J200, <span class="dv">0</span>)) <span class="sc">|&gt;</span></span>
<span id="cb24-10"><a href="#cb24-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-11"><a href="#cb24-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wret =</span> <span class="fu">coalesce</span>(ALSI_wret, <span class="dv">0</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb24-12"><a href="#cb24-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-13"><a href="#cb24-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wret =</span> <span class="fu">coalesce</span>(SWIX_wret, <span class="dv">0</span>)) <span class="sc">|&gt;</span></span>
<span id="cb24-14"><a href="#cb24-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-15"><a href="#cb24-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_ret_sector =</span> <span class="fu">sum</span>(ALSI_wret, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span>  <span class="co"># The weight-contribution of each sector on each day</span></span>
<span id="cb24-16"><a href="#cb24-16" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-17"><a href="#cb24-17" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_ret_sector =</span> <span class="fu">sum</span>(SWIX_wret, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span></span>
<span id="cb24-18"><a href="#cb24-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-19"><a href="#cb24-19" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wght_sector =</span> <span class="fu">sum</span>(J400, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span>  <span class="co"># The weighted return contribution of each sector on each day</span></span>
<span id="cb24-20"><a href="#cb24-20" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-21"><a href="#cb24-21" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wght_sector =</span> <span class="fu">sum</span>(J200, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span></span>
<span id="cb24-22"><a href="#cb24-22" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-23"><a href="#cb24-23" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>()</span>
<span id="cb24-24"><a href="#cb24-24" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-25"><a href="#cb24-25" aria-hidden="true" tabindex="-1"></a>ALSI_wght_sector <span class="ot">&lt;-</span>  df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,ALSI_wght_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-26"><a href="#cb24-26" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> ALSI_wght_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-27"><a href="#cb24-27" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-28"><a href="#cb24-28" aria-hidden="true" tabindex="-1"></a>SWIX_wght_sector <span class="ot">&lt;-</span> df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,SWIX_wght_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-29"><a href="#cb24-29" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> SWIX_wght_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-30"><a href="#cb24-30" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-31"><a href="#cb24-31" aria-hidden="true" tabindex="-1"></a>ALSI_ret_sector <span class="ot">&lt;-</span> df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,ALSI_ret_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-32"><a href="#cb24-32" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> ALSI_ret_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-33"><a href="#cb24-33" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-34"><a href="#cb24-34" aria-hidden="true" tabindex="-1"></a>SWIX_ret_sector <span class="ot">&lt;-</span> df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,SWIX_ret_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-35"><a href="#cb24-35" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> SWIX_ret_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-36"><a href="#cb24-36" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-37"><a href="#cb24-37" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-38"><a href="#cb24-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-39"><a href="#cb24-39" aria-hidden="true" tabindex="-1"></a> ALSI_RetPort_sector <span class="ot">&lt;-</span> </span>
<span id="cb24-40"><a href="#cb24-40" aria-hidden="true" tabindex="-1"></a>      rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(ALSI_ret_sector, </span>
<span id="cb24-41"><a href="#cb24-41" aria-hidden="true" tabindex="-1"></a>                                     </span>
<span id="cb24-42"><a href="#cb24-42" aria-hidden="true" tabindex="-1"></a>                       <span class="at">weights =</span> ALSI_wght_sector, <span class="at">lag_weights =</span> <span class="cn">TRUE</span>,</span>
<span id="cb24-43"><a href="#cb24-43" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-44"><a href="#cb24-44" aria-hidden="true" tabindex="-1"></a>                       <span class="at">verbose =</span> <span class="cn">TRUE</span>, <span class="at">contribution =</span> <span class="cn">TRUE</span>, </span>
<span id="cb24-45"><a href="#cb24-45" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-46"><a href="#cb24-46" aria-hidden="true" tabindex="-1"></a>                       <span class="at">value =</span> <span class="dv">1000</span>, <span class="at">geometric =</span> <span class="cn">TRUE</span>) </span>
<span id="cb24-47"><a href="#cb24-47" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-48"><a href="#cb24-48" aria-hidden="true" tabindex="-1"></a> SWIX_RetPort_sector <span class="ot">&lt;-</span> </span>
<span id="cb24-49"><a href="#cb24-49" aria-hidden="true" tabindex="-1"></a>      rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(SWIX_ret_sector, </span>
<span id="cb24-50"><a href="#cb24-50" aria-hidden="true" tabindex="-1"></a>                                     </span>
<span id="cb24-51"><a href="#cb24-51" aria-hidden="true" tabindex="-1"></a>                       <span class="at">weights =</span> SWIX_wght_sector, <span class="at">lag_weights =</span> <span class="cn">TRUE</span>,</span>
<span id="cb24-52"><a href="#cb24-52" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-53"><a href="#cb24-53" aria-hidden="true" tabindex="-1"></a>                       <span class="at">verbose =</span> <span class="cn">TRUE</span>, <span class="at">contribution =</span> <span class="cn">TRUE</span>, </span>
<span id="cb24-54"><a href="#cb24-54" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-55"><a href="#cb24-55" aria-hidden="true" tabindex="-1"></a>                       <span class="at">value =</span> <span class="dv">1000</span>, <span class="at">geometric =</span> <span class="cn">TRUE</span>)</span>
<span id="cb24-56"><a href="#cb24-56" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-57"><a href="#cb24-57" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-58"><a href="#cb24-58" aria-hidden="true" tabindex="-1"></a> ALSI_RetPort_sector<span class="sc">$</span>BOP.Weight  <span class="sc">%&gt;%</span> .[<span class="fu">endpoints</span>(.,<span class="st">&#39;months&#39;</span>)] <span class="sc">%&gt;%</span> <span class="fu">chart.StackedBar</span>()</span></code></pre></div>
<p><img
src="README_files/figure-html/Portfolio%20Returns%20subdivided%20into%20Market%20Caps%20Contributions-1.png" /><!-- --></p>
<div class="sourceCode" id="cb25"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb25-1"><a href="#cb25-1" aria-hidden="true" tabindex="-1"></a>  SWIX_RetPort_sector<span class="sc">$</span>BOP.Weight  <span class="sc">%&gt;%</span> .[<span class="fu">endpoints</span>(.,<span class="st">&#39;months&#39;</span>)] <span class="sc">%&gt;%</span> <span class="fu">chart.StackedBar</span>()</span></code></pre></div>
<p><img
src="README_files/figure-html/Portfolio%20Returns%20subdivided%20into%20Market%20Caps%20Contributions-2.png" /><!-- --></p>
<div class="sourceCode" id="cb26"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb26-1"><a href="#cb26-1" aria-hidden="true" tabindex="-1"></a>  ALSI_RetPort_sector<span class="sc">$</span>contribution <span class="sc">|&gt;</span> <span class="fu">chart.CumReturns</span>(<span class="at">legend.loc =</span> <span class="st">&quot;bottom&quot;</span>)</span></code></pre></div>
<p><img
src="README_files/figure-html/Portfolio%20Returns%20subdivided%20into%20Market%20Caps%20Contributions-3.png" /><!-- --></p>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb27-1"><a href="#cb27-1" aria-hidden="true" tabindex="-1"></a>  SWIX_RetPort_sector<span class="sc">$</span>contribution <span class="sc">|&gt;</span> <span class="fu">chart.CumReturns</span>(<span class="at">legend.loc =</span> <span class="st">&quot;bottom&quot;</span>)</span></code></pre></div>
<p><img
src="README_files/figure-html/Portfolio%20Returns%20subdivided%20into%20Market%20Caps%20Contributions-4.png" /><!-- --></p>
<div class="sourceCode" id="cb28"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb28-1"><a href="#cb28-1" aria-hidden="true" tabindex="-1"></a><span class="co"># I firt pull the effective rebalance dates</span></span>
<span id="cb28-2"><a href="#cb28-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb28-3"><a href="#cb28-3" aria-hidden="true" tabindex="-1"></a>Rebalance_Days <span class="ot">&lt;-</span>RebDays <span class="sc">|&gt;</span> <span class="fu">filter</span>(Date_Type <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;Effective Date&quot;</span>)) <span class="sc">|&gt;</span> <span class="fu">pull</span>(date)</span>
<span id="cb28-4"><a href="#cb28-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-5"><a href="#cb28-5" aria-hidden="true" tabindex="-1"></a><span class="co"># And now for both Indexes I create a capped weights tbl for rebalancing purposes</span></span>
<span id="cb28-6"><a href="#cb28-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb28-7"><a href="#cb28-7" aria-hidden="true" tabindex="-1"></a>rebalance_col_ALSI <span class="ot">&lt;-</span> T40_a <span class="sc">|&gt;</span> </span>
<span id="cb28-8"><a href="#cb28-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-9"><a href="#cb28-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">%in%</span> Rebalance_Days) <span class="sc">|&gt;</span> </span>
<span id="cb28-10"><a href="#cb28-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-11"><a href="#cb28-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(date, Tickers, J200) <span class="sc">|&gt;</span> </span>
<span id="cb28-12"><a href="#cb28-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-13"><a href="#cb28-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">rename</span>(<span class="at">weight =</span> J200) <span class="sc">|&gt;</span> </span>
<span id="cb28-14"><a href="#cb28-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-15"><a href="#cb28-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">RebalanceTime =</span> <span class="fu">format</span>(date, <span class="st">&quot;%Y_%b&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb28-16"><a href="#cb28-16" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-17"><a href="#cb28-17" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">weight=</span> <span class="fu">coalesce</span>(weight, <span class="dv">0</span>))</span>
<span id="cb28-18"><a href="#cb28-18" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-19"><a href="#cb28-19" aria-hidden="true" tabindex="-1"></a> rebalance_col_SWIX <span class="ot">&lt;-</span> T40_a <span class="sc">|&gt;</span> </span>
<span id="cb28-20"><a href="#cb28-20" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-21"><a href="#cb28-21" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">%in%</span> Rebalance_Days) <span class="sc">|&gt;</span> </span>
<span id="cb28-22"><a href="#cb28-22" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb28-23"><a href="#cb28-23" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(date, Tickers, J400) <span class="sc">|&gt;</span> </span>
<span id="cb28-24"><a href="#cb28-24" aria-hidden="true" tabindex="-1"></a>     </span>
<span id="cb28-25"><a href="#cb28-25" aria-hidden="true" tabindex="-1"></a>     <span class="fu">rename</span>(<span class="at">weight =</span> J400) <span class="sc">|&gt;</span> </span>
<span id="cb28-26"><a href="#cb28-26" aria-hidden="true" tabindex="-1"></a>     </span>
<span id="cb28-27"><a href="#cb28-27" aria-hidden="true" tabindex="-1"></a>     <span class="fu">mutate</span>(<span class="at">RebalanceTime =</span> <span class="fu">format</span>(date, <span class="st">&quot;%Y_%b&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb28-28"><a href="#cb28-28" aria-hidden="true" tabindex="-1"></a>     </span>
<span id="cb28-29"><a href="#cb28-29" aria-hidden="true" tabindex="-1"></a>      <span class="fu">mutate</span>(<span class="at">weight=</span> <span class="fu">coalesce</span>(weight, <span class="dv">0</span>))</span></code></pre></div>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb29-1"><a href="#cb29-1" aria-hidden="true" tabindex="-1"></a>Proportional_Cap_Foo <span class="ot">&lt;-</span> <span class="cf">function</span>(df_Cons, <span class="at">W_Cap =</span> <span class="fl">0.08</span>){</span>
<span id="cb29-2"><a href="#cb29-2" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-3"><a href="#cb29-3" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Let&#39;s require a specific form from the user... Alerting when it does not adhere this form</span></span>
<span id="cb29-4"><a href="#cb29-4" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>( <span class="sc">!</span><span class="st">&quot;weight&quot;</span> <span class="sc">%in%</span> <span class="fu">names</span>(df_Cons)) <span class="fu">stop</span>(<span class="st">&quot;... for Calc capping to work, provide weight column called &#39;weight&#39;&quot;</span>)</span>
<span id="cb29-5"><a href="#cb29-5" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-6"><a href="#cb29-6" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>( <span class="sc">!</span><span class="st">&quot;date&quot;</span> <span class="sc">%in%</span> <span class="fu">names</span>(df_Cons)) <span class="fu">stop</span>(<span class="st">&quot;... for Calc capping to work, provide date column called &#39;date&#39;&quot;</span>)</span>
<span id="cb29-7"><a href="#cb29-7" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-8"><a href="#cb29-8" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>( <span class="sc">!</span><span class="st">&quot;Tickers&quot;</span> <span class="sc">%in%</span> <span class="fu">names</span>(df_Cons)) <span class="fu">stop</span>(<span class="st">&quot;... for Calc capping to work, provide id column called &#39;Tickers&#39;&quot;</span>)</span>
<span id="cb29-9"><a href="#cb29-9" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-10"><a href="#cb29-10" aria-hidden="true" tabindex="-1"></a>  <span class="co"># First identify the cap breachers...</span></span>
<span id="cb29-11"><a href="#cb29-11" aria-hidden="true" tabindex="-1"></a>  Breachers <span class="ot">&lt;-</span> </span>
<span id="cb29-12"><a href="#cb29-12" aria-hidden="true" tabindex="-1"></a>    df_Cons <span class="sc">%&gt;%</span> <span class="fu">filter</span>(weight <span class="sc">&gt;</span> W_Cap) <span class="sc">%&gt;%</span> <span class="fu">pull</span>(Tickers)</span>
<span id="cb29-13"><a href="#cb29-13" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-14"><a href="#cb29-14" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Now keep track of breachers, and add to it to ensure they remain at 10%:</span></span>
<span id="cb29-15"><a href="#cb29-15" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>(<span class="fu">length</span>(Breachers) <span class="sc">&gt;</span> <span class="dv">0</span>) {</span>
<span id="cb29-16"><a href="#cb29-16" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-17"><a href="#cb29-17" aria-hidden="true" tabindex="-1"></a>    <span class="cf">while</span>( df_Cons <span class="sc">%&gt;%</span> <span class="fu">filter</span>(weight <span class="sc">&gt;</span> W_Cap) <span class="sc">%&gt;%</span> <span class="fu">nrow</span>() <span class="sc">&gt;</span> <span class="dv">0</span> ) {</span>
<span id="cb29-18"><a href="#cb29-18" aria-hidden="true" tabindex="-1"></a>      </span>
<span id="cb29-19"><a href="#cb29-19" aria-hidden="true" tabindex="-1"></a>      </span>
<span id="cb29-20"><a href="#cb29-20" aria-hidden="true" tabindex="-1"></a>      df_Cons <span class="ot">&lt;-</span></span>
<span id="cb29-21"><a href="#cb29-21" aria-hidden="true" tabindex="-1"></a>        </span>
<span id="cb29-22"><a href="#cb29-22" aria-hidden="true" tabindex="-1"></a>        <span class="fu">bind_rows</span>(</span>
<span id="cb29-23"><a href="#cb29-23" aria-hidden="true" tabindex="-1"></a>          </span>
<span id="cb29-24"><a href="#cb29-24" aria-hidden="true" tabindex="-1"></a>          df_Cons <span class="sc">%&gt;%</span> <span class="fu">filter</span>(Tickers <span class="sc">%in%</span> Breachers) <span class="sc">%&gt;%</span> <span class="fu">mutate</span>(<span class="at">weight =</span> W_Cap),</span>
<span id="cb29-25"><a href="#cb29-25" aria-hidden="true" tabindex="-1"></a>          </span>
<span id="cb29-26"><a href="#cb29-26" aria-hidden="true" tabindex="-1"></a>          df_Cons <span class="sc">%&gt;%</span> <span class="fu">filter</span>(<span class="sc">!</span>Tickers <span class="sc">%in%</span> Breachers) <span class="sc">%&gt;%</span> </span>
<span id="cb29-27"><a href="#cb29-27" aria-hidden="true" tabindex="-1"></a>            <span class="fu">mutate</span>(<span class="at">weight =</span> (weight <span class="sc">/</span> <span class="fu">sum</span>(weight, <span class="at">na.rm=</span>T)) <span class="sc">*</span> (<span class="dv">1</span><span class="sc">-</span><span class="fu">length</span>(Breachers)<span class="sc">*</span>W_Cap) )</span>
<span id="cb29-28"><a href="#cb29-28" aria-hidden="true" tabindex="-1"></a>          </span>
<span id="cb29-29"><a href="#cb29-29" aria-hidden="true" tabindex="-1"></a>        )</span>
<span id="cb29-30"><a href="#cb29-30" aria-hidden="true" tabindex="-1"></a>      </span>
<span id="cb29-31"><a href="#cb29-31" aria-hidden="true" tabindex="-1"></a>      Breachers <span class="ot">&lt;-</span> <span class="fu">c</span>(Breachers, df_Cons <span class="sc">%&gt;%</span> <span class="fu">filter</span>(weight <span class="sc">&gt;</span> W_Cap) <span class="sc">%&gt;%</span> <span class="fu">pull</span>(Tickers))</span>
<span id="cb29-32"><a href="#cb29-32" aria-hidden="true" tabindex="-1"></a>      </span>
<span id="cb29-33"><a href="#cb29-33" aria-hidden="true" tabindex="-1"></a>    }</span>
<span id="cb29-34"><a href="#cb29-34" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-35"><a href="#cb29-35" aria-hidden="true" tabindex="-1"></a>    <span class="cf">if</span>( <span class="fu">sum</span>(df_Cons<span class="sc">$</span>weight, <span class="at">na.rm=</span>T) <span class="sc">&gt;</span> <span class="fl">1.001</span> <span class="sc">|</span> <span class="fu">sum</span>(df_Cons<span class="sc">$</span>weight, <span class="at">na.rm=</span>T) <span class="sc">&lt;</span> <span class="fl">0.999</span> <span class="sc">|</span> <span class="fu">max</span>(df_Cons<span class="sc">$</span>weight, <span class="at">na.rm =</span> T) <span class="sc">&gt;</span> W_Cap) {</span>
<span id="cb29-36"><a href="#cb29-36" aria-hidden="true" tabindex="-1"></a>      </span>
<span id="cb29-37"><a href="#cb29-37" aria-hidden="true" tabindex="-1"></a>      <span class="fu">stop</span>( glue<span class="sc">::</span><span class="fu">glue</span>(<span class="st">&quot;For the Generic weight trimming function used: the weight trimming causes non unit </span></span>
<span id="cb29-38"><a href="#cb29-38" aria-hidden="true" tabindex="-1"></a><span class="st">      summation of weights for date: {unique(df_Cons$date)}...</span><span class="sc">\n</span></span>
<span id="cb29-39"><a href="#cb29-39" aria-hidden="true" tabindex="-1"></a><span class="st">      The restriction could be too low or some dates have extreme concentrations...&quot;</span>) )</span>
<span id="cb29-40"><a href="#cb29-40" aria-hidden="true" tabindex="-1"></a>      </span>
<span id="cb29-41"><a href="#cb29-41" aria-hidden="true" tabindex="-1"></a>    }</span>
<span id="cb29-42"><a href="#cb29-42" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-43"><a href="#cb29-43" aria-hidden="true" tabindex="-1"></a>  } <span class="cf">else</span> {</span>
<span id="cb29-44"><a href="#cb29-44" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-45"><a href="#cb29-45" aria-hidden="true" tabindex="-1"></a>  }</span>
<span id="cb29-46"><a href="#cb29-46" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-47"><a href="#cb29-47" aria-hidden="true" tabindex="-1"></a>  df_Cons</span>
<span id="cb29-48"><a href="#cb29-48" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-49"><a href="#cb29-49" aria-hidden="true" tabindex="-1"></a>  }</span>
<span id="cb29-50"><a href="#cb29-50" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-51"><a href="#cb29-51" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-52"><a href="#cb29-52" aria-hidden="true" tabindex="-1"></a><span class="co"># Now, to map this across all the dates, I purrr::map_df </span></span>
<span id="cb29-53"><a href="#cb29-53" aria-hidden="true" tabindex="-1"></a>Capped_ALSI_10 <span class="ot">&lt;-</span> </span>
<span id="cb29-54"><a href="#cb29-54" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-55"><a href="#cb29-55" aria-hidden="true" tabindex="-1"></a>    rebalance_col_ALSI <span class="sc">|&gt;</span> </span>
<span id="cb29-56"><a href="#cb29-56" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-57"><a href="#cb29-57" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_split</span>(RebalanceTime) <span class="sc">|&gt;</span> </span>
<span id="cb29-58"><a href="#cb29-58" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-59"><a href="#cb29-59" aria-hidden="true" tabindex="-1"></a>    <span class="fu">map_df</span>(<span class="sc">~</span><span class="fu">Proportional_Cap_Foo</span>(., <span class="at">W_Cap =</span> <span class="fl">0.1</span>) ) <span class="sc">|&gt;</span>  <span class="fu">select</span>(<span class="sc">-</span>RebalanceTime)</span>
<span id="cb29-60"><a href="#cb29-60" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-61"><a href="#cb29-61" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I do the same for a 6% cap:</span></span>
<span id="cb29-62"><a href="#cb29-62" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-63"><a href="#cb29-63" aria-hidden="true" tabindex="-1"></a>Capped_ALSI_6 <span class="ot">&lt;-</span> </span>
<span id="cb29-64"><a href="#cb29-64" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-65"><a href="#cb29-65" aria-hidden="true" tabindex="-1"></a>    rebalance_col_ALSI <span class="sc">|&gt;</span> </span>
<span id="cb29-66"><a href="#cb29-66" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-67"><a href="#cb29-67" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_split</span>(RebalanceTime) <span class="sc">|&gt;</span> </span>
<span id="cb29-68"><a href="#cb29-68" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-69"><a href="#cb29-69" aria-hidden="true" tabindex="-1"></a>    <span class="fu">map_df</span>(<span class="sc">~</span><span class="fu">Proportional_Cap_Foo</span>(., <span class="at">W_Cap =</span> <span class="fl">0.06</span>) ) <span class="sc">|&gt;</span>  <span class="fu">select</span>(<span class="sc">-</span>RebalanceTime)</span>
<span id="cb29-70"><a href="#cb29-70" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-71"><a href="#cb29-71" aria-hidden="true" tabindex="-1"></a>Capped_SWIX_10 <span class="ot">&lt;-</span> </span>
<span id="cb29-72"><a href="#cb29-72" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-73"><a href="#cb29-73" aria-hidden="true" tabindex="-1"></a>    rebalance_col_ALSI <span class="sc">|&gt;</span> </span>
<span id="cb29-74"><a href="#cb29-74" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-75"><a href="#cb29-75" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_split</span>(RebalanceTime) <span class="sc">|&gt;</span> </span>
<span id="cb29-76"><a href="#cb29-76" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-77"><a href="#cb29-77" aria-hidden="true" tabindex="-1"></a>    <span class="fu">map_df</span>(<span class="sc">~</span><span class="fu">Proportional_Cap_Foo</span>(., <span class="at">W_Cap =</span> <span class="fl">0.1</span>) ) <span class="sc">|&gt;</span>  <span class="fu">select</span>(<span class="sc">-</span>RebalanceTime)</span>
<span id="cb29-78"><a href="#cb29-78" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb29-79"><a href="#cb29-79" aria-hidden="true" tabindex="-1"></a>Capped_SWIX_6 <span class="ot">&lt;-</span> </span>
<span id="cb29-80"><a href="#cb29-80" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-81"><a href="#cb29-81" aria-hidden="true" tabindex="-1"></a>    rebalance_col_ALSI <span class="sc">|&gt;</span> </span>
<span id="cb29-82"><a href="#cb29-82" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-83"><a href="#cb29-83" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_split</span>(RebalanceTime) <span class="sc">|&gt;</span> </span>
<span id="cb29-84"><a href="#cb29-84" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb29-85"><a href="#cb29-85" aria-hidden="true" tabindex="-1"></a>    <span class="fu">map_df</span>(<span class="sc">~</span><span class="fu">Proportional_Cap_Foo</span>(., <span class="at">W_Cap =</span> <span class="fl">0.06</span>) ) <span class="sc">|&gt;</span>  <span class="fu">select</span>(<span class="sc">-</span>RebalanceTime)</span>
<span id="cb29-86"><a href="#cb29-86" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-87"><a href="#cb29-87" aria-hidden="true" tabindex="-1"></a><span class="co"># # Testing if the max weight is correct for all 4 tbl above</span></span>
<span id="cb29-88"><a href="#cb29-88" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb29-89"><a href="#cb29-89" aria-hidden="true" tabindex="-1"></a>Capped_ALSI_10 <span class="sc">%&gt;%</span> <span class="fu">pull</span>(weight) <span class="sc">%&gt;%</span> <span class="fu">max</span>(.) </span></code></pre></div>
<pre><code>## [1] 0.1</code></pre>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb31-1"><a href="#cb31-1" aria-hidden="true" tabindex="-1"></a>Capped_ALSI_6 <span class="sc">%&gt;%</span> <span class="fu">pull</span>(weight) <span class="sc">%&gt;%</span> <span class="fu">max</span>(.) </span></code></pre></div>
<pre><code>## [1] 0.06</code></pre>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb33-1"><a href="#cb33-1" aria-hidden="true" tabindex="-1"></a>Capped_SWIX_10 <span class="sc">%&gt;%</span> <span class="fu">pull</span>(weight) <span class="sc">%&gt;%</span> <span class="fu">max</span>(.) </span></code></pre></div>
<pre><code>## [1] 0.1</code></pre>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb35-1"><a href="#cb35-1" aria-hidden="true" tabindex="-1"></a>Capped_SWIX_6 <span class="sc">%&gt;%</span> <span class="fu">pull</span>(weight) <span class="sc">%&gt;%</span> <span class="fu">max</span>(.) <span class="co"># Success!!</span></span></code></pre></div>
<pre><code>## [1] 0.06</code></pre>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb37-1"><a href="#cb37-1" aria-hidden="true" tabindex="-1"></a><span class="do">####For ALSI capped at 10%#####</span></span>
<span id="cb37-2"><a href="#cb37-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-3"><a href="#cb37-3" aria-hidden="true" tabindex="-1"></a>wghts_ALSI_10 <span class="ot">&lt;-</span> </span>
<span id="cb37-4"><a href="#cb37-4" aria-hidden="true" tabindex="-1"></a>  Capped_ALSI_10 <span class="sc">%&gt;%</span> </span>
<span id="cb37-5"><a href="#cb37-5" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> weight, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-6"><a href="#cb37-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-7"><a href="#cb37-7" aria-hidden="true" tabindex="-1"></a>ret_ALSI_10 <span class="ot">&lt;-</span> </span>
<span id="cb37-8"><a href="#cb37-8" aria-hidden="true" tabindex="-1"></a>  T40_a <span class="sc">%&gt;%</span> </span>
<span id="cb37-9"><a href="#cb37-9" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-10"><a href="#cb37-10" aria-hidden="true" tabindex="-1"></a>  <span class="fu">filter</span>(Tickers <span class="sc">%in%</span> <span class="fu">unique</span>(Capped_ALSI_10<span class="sc">$</span>Tickers) ) <span class="sc">%&gt;%</span> </span>
<span id="cb37-11"><a href="#cb37-11" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-12"><a href="#cb37-12" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Return, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-13"><a href="#cb37-13" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-14"><a href="#cb37-14" aria-hidden="true" tabindex="-1"></a>wghts_ALSI_10[<span class="fu">is.na</span>(wghts_ALSI_10)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-15"><a href="#cb37-15" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-16"><a href="#cb37-16" aria-hidden="true" tabindex="-1"></a>ret_ALSI_10[<span class="fu">is.na</span>(ret_ALSI_10)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-17"><a href="#cb37-17" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-18"><a href="#cb37-18" aria-hidden="true" tabindex="-1"></a>ALSI_10_Idx <span class="ot">&lt;-</span> </span>
<span id="cb37-19"><a href="#cb37-19" aria-hidden="true" tabindex="-1"></a>  rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(<span class="at">R =</span> ret_ALSI_10, <span class="at">weights =</span> wghts_ALSI_10, <span class="at">lag_weights =</span> T) <span class="sc">|&gt;</span> </span>
<span id="cb37-20"><a href="#cb37-20" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-21"><a href="#cb37-21" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Then I make it a tibble:</span></span>
<span id="cb37-22"><a href="#cb37-22" aria-hidden="true" tabindex="-1"></a>  <span class="fu">xts_tbl</span>() <span class="sc">|&gt;</span>  </span>
<span id="cb37-23"><a href="#cb37-23" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-24"><a href="#cb37-24" aria-hidden="true" tabindex="-1"></a>  <span class="fu">rename</span>(<span class="at">ALSI_10_Idx =</span> portfolio.returns)</span>
<span id="cb37-25"><a href="#cb37-25" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-26"><a href="#cb37-26" aria-hidden="true" tabindex="-1"></a><span class="do">####For ALSI capped at 6%#####</span></span>
<span id="cb37-27"><a href="#cb37-27" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-28"><a href="#cb37-28" aria-hidden="true" tabindex="-1"></a>wghts_ALSI_6 <span class="ot">&lt;-</span> </span>
<span id="cb37-29"><a href="#cb37-29" aria-hidden="true" tabindex="-1"></a>  Capped_ALSI_6 <span class="sc">%&gt;%</span> </span>
<span id="cb37-30"><a href="#cb37-30" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> weight, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-31"><a href="#cb37-31" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-32"><a href="#cb37-32" aria-hidden="true" tabindex="-1"></a>ret_ALSI_6 <span class="ot">&lt;-</span> </span>
<span id="cb37-33"><a href="#cb37-33" aria-hidden="true" tabindex="-1"></a>  T40_a <span class="sc">%&gt;%</span> </span>
<span id="cb37-34"><a href="#cb37-34" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-35"><a href="#cb37-35" aria-hidden="true" tabindex="-1"></a>  <span class="fu">filter</span>(Tickers <span class="sc">%in%</span> <span class="fu">unique</span>(Capped_ALSI_6<span class="sc">$</span>Tickers) ) <span class="sc">%&gt;%</span> </span>
<span id="cb37-36"><a href="#cb37-36" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-37"><a href="#cb37-37" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Return, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-38"><a href="#cb37-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-39"><a href="#cb37-39" aria-hidden="true" tabindex="-1"></a>wghts_ALSI_6[<span class="fu">is.na</span>(wghts_ALSI_6)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-40"><a href="#cb37-40" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-41"><a href="#cb37-41" aria-hidden="true" tabindex="-1"></a>ret_ALSI_6[<span class="fu">is.na</span>(ret_ALSI_6)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-42"><a href="#cb37-42" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-43"><a href="#cb37-43" aria-hidden="true" tabindex="-1"></a>ALSI_6_Idx <span class="ot">&lt;-</span> </span>
<span id="cb37-44"><a href="#cb37-44" aria-hidden="true" tabindex="-1"></a>  rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(<span class="at">R =</span> ret_ALSI_6, <span class="at">weights =</span> wghts_ALSI_6, <span class="at">lag_weights =</span> T) <span class="sc">|&gt;</span> </span>
<span id="cb37-45"><a href="#cb37-45" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-46"><a href="#cb37-46" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Then I make it a tibble:</span></span>
<span id="cb37-47"><a href="#cb37-47" aria-hidden="true" tabindex="-1"></a>  <span class="fu">xts_tbl</span>() <span class="sc">|&gt;</span>  </span>
<span id="cb37-48"><a href="#cb37-48" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-49"><a href="#cb37-49" aria-hidden="true" tabindex="-1"></a>  <span class="fu">rename</span>(<span class="at">ALSI_6_Idx =</span> portfolio.returns)</span>
<span id="cb37-50"><a href="#cb37-50" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-51"><a href="#cb37-51" aria-hidden="true" tabindex="-1"></a><span class="do">####For SWIX capped at 10%#####</span></span>
<span id="cb37-52"><a href="#cb37-52" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-53"><a href="#cb37-53" aria-hidden="true" tabindex="-1"></a>wghts_SWIX_10 <span class="ot">&lt;-</span> </span>
<span id="cb37-54"><a href="#cb37-54" aria-hidden="true" tabindex="-1"></a>  Capped_SWIX_10 <span class="sc">%&gt;%</span> </span>
<span id="cb37-55"><a href="#cb37-55" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> weight, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-56"><a href="#cb37-56" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-57"><a href="#cb37-57" aria-hidden="true" tabindex="-1"></a>ret_SWIX_10 <span class="ot">&lt;-</span> </span>
<span id="cb37-58"><a href="#cb37-58" aria-hidden="true" tabindex="-1"></a>  T40_a <span class="sc">%&gt;%</span> </span>
<span id="cb37-59"><a href="#cb37-59" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-60"><a href="#cb37-60" aria-hidden="true" tabindex="-1"></a>  <span class="fu">filter</span>(Tickers <span class="sc">%in%</span> <span class="fu">unique</span>(Capped_SWIX_10<span class="sc">$</span>Tickers) ) <span class="sc">%&gt;%</span> </span>
<span id="cb37-61"><a href="#cb37-61" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-62"><a href="#cb37-62" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Return, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-63"><a href="#cb37-63" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-64"><a href="#cb37-64" aria-hidden="true" tabindex="-1"></a>wghts_SWIX_10[<span class="fu">is.na</span>(wghts_SWIX_10)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-65"><a href="#cb37-65" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-66"><a href="#cb37-66" aria-hidden="true" tabindex="-1"></a>ret_SWIX_10[<span class="fu">is.na</span>(ret_SWIX_10)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-67"><a href="#cb37-67" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-68"><a href="#cb37-68" aria-hidden="true" tabindex="-1"></a>SWIX_10_Idx <span class="ot">&lt;-</span> </span>
<span id="cb37-69"><a href="#cb37-69" aria-hidden="true" tabindex="-1"></a>  rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(<span class="at">R =</span> ret_SWIX_10, <span class="at">weights =</span> wghts_SWIX_10, <span class="at">lag_weights =</span> T) <span class="sc">|&gt;</span> </span>
<span id="cb37-70"><a href="#cb37-70" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-71"><a href="#cb37-71" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Then I make it a tibble:</span></span>
<span id="cb37-72"><a href="#cb37-72" aria-hidden="true" tabindex="-1"></a>  <span class="fu">xts_tbl</span>() <span class="sc">|&gt;</span>  </span>
<span id="cb37-73"><a href="#cb37-73" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-74"><a href="#cb37-74" aria-hidden="true" tabindex="-1"></a>  <span class="fu">rename</span>(<span class="at">SWIX_10_Idx =</span> portfolio.returns)</span>
<span id="cb37-75"><a href="#cb37-75" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-76"><a href="#cb37-76" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-77"><a href="#cb37-77" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-78"><a href="#cb37-78" aria-hidden="true" tabindex="-1"></a><span class="do">####For SWIX capped at 6%#####</span></span>
<span id="cb37-79"><a href="#cb37-79" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-80"><a href="#cb37-80" aria-hidden="true" tabindex="-1"></a>wghts_SWIX_6 <span class="ot">&lt;-</span> </span>
<span id="cb37-81"><a href="#cb37-81" aria-hidden="true" tabindex="-1"></a>  Capped_SWIX_6 <span class="sc">%&gt;%</span> </span>
<span id="cb37-82"><a href="#cb37-82" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> weight, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-83"><a href="#cb37-83" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-84"><a href="#cb37-84" aria-hidden="true" tabindex="-1"></a>ret_SWIX_6 <span class="ot">&lt;-</span> </span>
<span id="cb37-85"><a href="#cb37-85" aria-hidden="true" tabindex="-1"></a>  T40_a <span class="sc">%&gt;%</span> </span>
<span id="cb37-86"><a href="#cb37-86" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-87"><a href="#cb37-87" aria-hidden="true" tabindex="-1"></a>  <span class="fu">filter</span>(Tickers <span class="sc">%in%</span> <span class="fu">unique</span>(Capped_SWIX_6<span class="sc">$</span>Tickers) ) <span class="sc">%&gt;%</span> </span>
<span id="cb37-88"><a href="#cb37-88" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-89"><a href="#cb37-89" aria-hidden="true" tabindex="-1"></a>  <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Return, <span class="at">spread_by =</span> Tickers)</span>
<span id="cb37-90"><a href="#cb37-90" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-91"><a href="#cb37-91" aria-hidden="true" tabindex="-1"></a>wghts_SWIX_6[<span class="fu">is.na</span>(wghts_SWIX_6)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-92"><a href="#cb37-92" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-93"><a href="#cb37-93" aria-hidden="true" tabindex="-1"></a>ret_SWIX_6[<span class="fu">is.na</span>(ret_SWIX_6)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb37-94"><a href="#cb37-94" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-95"><a href="#cb37-95" aria-hidden="true" tabindex="-1"></a>SWIX_6_Idx <span class="ot">&lt;-</span> </span>
<span id="cb37-96"><a href="#cb37-96" aria-hidden="true" tabindex="-1"></a>  rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(<span class="at">R =</span> ret_SWIX_6, <span class="at">weights =</span> wghts_SWIX_6, <span class="at">lag_weights =</span> T) <span class="sc">|&gt;</span> </span>
<span id="cb37-97"><a href="#cb37-97" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-98"><a href="#cb37-98" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Then I make it a tibble:</span></span>
<span id="cb37-99"><a href="#cb37-99" aria-hidden="true" tabindex="-1"></a>  <span class="fu">xts_tbl</span>() <span class="sc">|&gt;</span>  </span>
<span id="cb37-100"><a href="#cb37-100" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb37-101"><a href="#cb37-101" aria-hidden="true" tabindex="-1"></a>  <span class="fu">rename</span>(<span class="at">SWIX_6_Idx =</span> portfolio.returns)</span></code></pre></div>
<div class="sourceCode" id="cb38"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb38-1"><a href="#cb38-1" aria-hidden="true" tabindex="-1"></a>Capped_df_final <span class="ot">&lt;-</span> ALSI_6_Idx <span class="sc">|&gt;</span> <span class="fu">inner_join</span>(ALSI_10_Idx, <span class="at">by =</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb38-2"><a href="#cb38-2" aria-hidden="true" tabindex="-1"></a>    <span class="fu">inner_join</span>(SWIX_6_Idx, <span class="at">by =</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb38-3"><a href="#cb38-3" aria-hidden="true" tabindex="-1"></a>    <span class="fu">inner_join</span>(SWIX_10_Idx, <span class="at">by =</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span></span>
<span id="cb38-4"><a href="#cb38-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb38-5"><a href="#cb38-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb38-6"><a href="#cb38-6" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="fu">across</span>(<span class="at">.cols =</span> <span class="sc">-</span>date, <span class="at">.fns =</span> <span class="sc">~</span><span class="fu">cumprod</span>(<span class="dv">1</span><span class="sc">+</span>.))) <span class="sc">|&gt;</span> <span class="co"># cumulative returns</span></span>
<span id="cb38-7"><a href="#cb38-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="fu">across</span>(<span class="at">.cols =</span> <span class="sc">-</span>date, <span class="at">.fns =</span> <span class="sc">~</span>.<span class="sc">/</span><span class="fu">first</span>(.))) <span class="sc">|&gt;</span>   <span class="co"># Start at 1</span></span>
<span id="cb38-8"><a href="#cb38-8" aria-hidden="true" tabindex="-1"></a>    <span class="fu">inner_join</span>(Cum_ret <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from=</span> <span class="st">&quot;Index&quot;</span>, <span class="at">values_from=</span> <span class="st">&quot;Cumret&quot;</span>), <span class="at">by =</span> <span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> <span class="co"># Add the uncapped cumret </span></span>
<span id="cb38-9"><a href="#cb38-9" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb38-10"><a href="#cb38-10" aria-hidden="true" tabindex="-1"></a>    <span class="fu">rename</span>(<span class="st">&quot;ALSI Capped 6%&quot;</span> <span class="ot">=</span> ALSI_6_Idx, <span class="st">&quot;ALSI Capped 10%&quot;</span> <span class="ot">=</span> ALSI_10_Idx,<span class="st">&quot;SWIX Capped 6%&quot;</span> <span class="ot">=</span>  SWIX_6_Idx, <span class="st">&quot;SWIX Capped 10%&quot;</span> <span class="ot">=</span>  SWIX_10_Idx, <span class="st">&quot;Uncapped SWIX&quot;</span> <span class="ot">=</span> SWIX, <span class="st">&quot;Uncapped ALSI&quot;</span> <span class="ot">=</span> ALSI) <span class="sc">|&gt;</span> <span class="co"># rename for clarity</span></span>
<span id="cb38-11"><a href="#cb38-11" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb38-12"><a href="#cb38-12" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Description&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Values&quot;</span>)</span>
<span id="cb38-13"><a href="#cb38-13" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-14"><a href="#cb38-14" aria-hidden="true" tabindex="-1"></a><span class="co"># And now, at last, for the plot</span></span>
<span id="cb38-15"><a href="#cb38-15" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-16"><a href="#cb38-16" aria-hidden="true" tabindex="-1"></a>capping_plot_ALSI <span class="ot">&lt;-</span> </span>
<span id="cb38-17"><a href="#cb38-17" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-18"><a href="#cb38-18" aria-hidden="true" tabindex="-1"></a>Capped_df_final <span class="sc">|&gt;</span> <span class="fu">filter</span>(Description <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;ALSI Capped 6%&quot;</span>, <span class="st">&quot;ALSI Capped 10%&quot;</span>, <span class="st">&quot;Uncapped ALSI&quot;</span>))  <span class="sc">|&gt;</span> </span>
<span id="cb38-19"><a href="#cb38-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-20"><a href="#cb38-20" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb38-21"><a href="#cb38-21" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb38-22"><a href="#cb38-22" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Values , <span class="at">color =</span> Description), <span class="at">size =</span> <span class="fl">0.8</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb38-23"><a href="#cb38-23" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb38-24"><a href="#cb38-24" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb38-25"><a href="#cb38-25" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb38-26"><a href="#cb38-26" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb38-27"><a href="#cb38-27" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb38-28"><a href="#cb38-28" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb38-29"><a href="#cb38-29" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb38-30"><a href="#cb38-30" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb38-31"><a href="#cb38-31" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb38-32"><a href="#cb38-32" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Cumulative Returns of ALSI and SWIX Indexes Capped at 6% and 10%&quot;</span>,</span>
<span id="cb38-33"><a href="#cb38-33" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb38-34"><a href="#cb38-34" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-35"><a href="#cb38-35" aria-hidden="true" tabindex="-1"></a>capping_plot_SWIX <span class="ot">&lt;-</span> </span>
<span id="cb38-36"><a href="#cb38-36" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-37"><a href="#cb38-37" aria-hidden="true" tabindex="-1"></a>Capped_df_final <span class="sc">|&gt;</span> <span class="fu">filter</span>(Description <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;SWIX Capped 6%&quot;</span>, <span class="st">&quot;SWIX Capped 10%&quot;</span>, <span class="st">&quot;Uncapped SWIX&quot;</span>))  <span class="sc">|&gt;</span> </span>
<span id="cb38-38"><a href="#cb38-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-39"><a href="#cb38-39" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb38-40"><a href="#cb38-40" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb38-41"><a href="#cb38-41" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Values , <span class="at">color =</span> Description), <span class="at">size =</span> <span class="fl">0.8</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb38-42"><a href="#cb38-42" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb38-43"><a href="#cb38-43" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb38-44"><a href="#cb38-44" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb38-45"><a href="#cb38-45" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb38-46"><a href="#cb38-46" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb38-47"><a href="#cb38-47" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb38-48"><a href="#cb38-48" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb38-49"><a href="#cb38-49" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb38-50"><a href="#cb38-50" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb38-51"><a href="#cb38-51" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Cumulative Returns of ALSI and SWIX Indexes Capped at 6% and 10%&quot;</span>,</span>
<span id="cb38-52"><a href="#cb38-52" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb38-53"><a href="#cb38-53" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-54"><a href="#cb38-54" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb38-55"><a href="#cb38-55" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb38-56"><a href="#cb38-56" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(capping_plot_ALSI, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;1 year&quot;</span>, <span class="at">darkcol =</span> T)</span></code></pre></div>
<pre><code>## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.</code></pre>
<p><img
src="README_files/figure-html/Plot%20the%20capped%20indexes-1.png" /><!-- --></p>
<div class="sourceCode" id="cb40"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb40-1"><a href="#cb40-1" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(capping_plot_SWIX, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;1 year&quot;</span>, <span class="at">darkcol =</span> T)</span></code></pre></div>
<pre><code>## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.</code></pre>
<p><img
src="README_files/figure-html/Plot%20the%20capped%20indexes-2.png" /><!-- --></p>
