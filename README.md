<h1 id="purpose">Purpose</h1>
<p>Purpose of this work folder.</p>
<p>Ideally store a minimum working example data set in data folder.</p>
<p>Add binary files in bin, and closed R functions in code. Human
Readable settings files (e.g. csv) should be placed in settings/</p>
<pre><code>##          used (Mb) gc trigger (Mb) max used (Mb)
## Ncells 472630 25.3    1028248   55   644245 34.5
## Vcells 844024  6.5    8388608   64  1635428 12.5</code></pre>
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
