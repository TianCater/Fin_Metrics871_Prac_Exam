<h1 id="purpose">Purpose</h1>
<p>The purpose of this work folder is to provide a sanitized workflow
for the code and data wrangling underlying the Financial Econometrics
871 practical exam before compiling the final reports in pdf formats as
given above.</p>
<p>To get started..</p>
<pre><code>##          used (Mb) gc trigger (Mb) max used (Mb)
## Ncells 473719 25.3    1031360 55.1   644245 34.5
## Vcells 855344  6.6    8388608 64.0  1635428 12.5</code></pre>
<pre><code>## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.4.0      ✔ purrr   0.3.5 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
## ✔ readr   2.1.3      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()</code></pre>
<h1 id="question-1">Question 1</h1>
<p>The purpose of this question is to prepare a presentation where I
showcast the performance of my fund (called Our Fund ), specifically
putting my fund’s performance into perspective by comparing it to the
benchmark (Capped SWIX) as well as to industry peers (ASISA active
managers).</p>
<h2 id="loading-data">Loading data</h2>
<div class="sourceCode" id="cb3"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb3-1"><a href="#cb3-1" aria-hidden="true" tabindex="-1"></a>ASISA <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/ASISA.rds&quot;</span>) <span class="co"># ASISA Active Managers. Notice that there are 227 different actively managed funds.</span></span>
<span id="cb3-2"><a href="#cb3-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb3-3"><a href="#cb3-3" aria-hidden="true" tabindex="-1"></a>                                    <span class="co"># Monthly observations, 2002-11-30 to 2022-10-31</span></span>
<span id="cb3-4"><a href="#cb3-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb3-5"><a href="#cb3-5" aria-hidden="true" tabindex="-1"></a>BM <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/Capped_SWIX.rds&quot;</span>) <span class="co"># Benchmark: Capped Swix. Monthly observations, 1999-12-31 to 2022-10-31</span></span>
<span id="cb3-6"><a href="#cb3-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb3-7"><a href="#cb3-7" aria-hidden="true" tabindex="-1"></a>AI_Fund <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/AI_Max_Fund.rds&quot;</span>) <span class="co"># My Systematic AI Fund. Monthly observations, 2003-01-31 to 2022-10-31</span></span></code></pre></div>
<h2 id="rolling-3-year-annualized-returns-comparisson">Rolling 3 Year
Annualized Returns Comparisson</h2>
<p>To plot the Rolling 3 Year Annualized Returns, I start by merging the
three datasets, calculate the Rolling 3 Year Annualized Returns,
determine the 90, 75, 50, 25, and 10 percent percentiles. Thereafter, I
graph the rolling returns of Our Fund versus the benchmark Capped SWIX
(J433) versus the ASISA Percentiles.</p>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a>pacman<span class="sc">::</span><span class="fu">p_load</span>(RcppRoll)</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-3"><a href="#cb4-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-4"><a href="#cb4-4" aria-hidden="true" tabindex="-1"></a><span class="co"># I start by merging the three datasets</span></span>
<span id="cb4-5"><a href="#cb4-5" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-6"><a href="#cb4-6" aria-hidden="true" tabindex="-1"></a>df <span class="ot">&lt;-</span> <span class="fu">left_join</span>(AI_Fund <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Tickers&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Returns&quot;</span>), BM <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Tickers&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Returns&quot;</span>) ,<span class="at">by =</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> <span class="fu">left_join</span>(ASISA <span class="sc">|&gt;</span> <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Returns&quot;</span>), <span class="at">by=</span><span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb4-7"><a href="#cb4-7" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-8"><a href="#cb4-8" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Returns&quot;</span>)</span>
<span id="cb4-9"><a href="#cb4-9" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-10"><a href="#cb4-10" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-11"><a href="#cb4-11" aria-hidden="true" tabindex="-1"></a><span class="co"># I calculate the rolling returns on a 3 year annualized basis</span></span>
<span id="cb4-12"><a href="#cb4-12" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-13"><a href="#cb4-13" aria-hidden="true" tabindex="-1"></a>df_rolling <span class="ot">&lt;-</span> df <span class="sc">|&gt;</span> <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Name) <span class="sc">|&gt;</span> </span>
<span id="cb4-14"><a href="#cb4-14" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Roll_rets =</span> RcppRoll<span class="sc">::</span><span class="fu">roll_prod</span>(<span class="dv">1</span><span class="sc">+</span> Returns, <span class="dv">36</span>, <span class="at">fill =</span> <span class="cn">NA</span>, <span class="at">align =</span> <span class="st">&quot;right&quot;</span>)<span class="sc">^</span>(<span class="dv">12</span><span class="sc">/</span><span class="dv">36</span>) <span class="sc">-</span><span class="dv">1</span>) <span class="sc">|&gt;</span> </span>
<span id="cb4-15"><a href="#cb4-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb4-16"><a href="#cb4-16" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(<span class="fu">any</span>(<span class="sc">!</span><span class="fu">is.na</span>(Roll_rets))) <span class="sc">|&gt;</span> <span class="co"># Remove dates with no rolling returns</span></span>
<span id="cb4-17"><a href="#cb4-17" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>()</span>
<span id="cb4-18"><a href="#cb4-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-19"><a href="#cb4-19" aria-hidden="true" tabindex="-1"></a><span class="co"># Next, I determine the top decile, top quartile, median, bottom decile and bottom quartile for the ASISA funds for each month</span></span>
<span id="cb4-20"><a href="#cb4-20" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-21"><a href="#cb4-21" aria-hidden="true" tabindex="-1"></a>ASISA_percentiles <span class="ot">&lt;-</span> df_rolling <span class="sc">|&gt;</span> <span class="fu">filter</span>(<span class="sc">!</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;Our_Fund&quot;</span>, <span class="st">&quot;J433&quot;</span>)) ) <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="sc">-</span>Returns) <span class="sc">|&gt;</span> </span>
<span id="cb4-22"><a href="#cb4-22" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-23"><a href="#cb4-23" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="st">&quot;Top decile&quot;</span> <span class="ot">=</span> <span class="fu">quantile</span>(Roll_rets ,<span class="at">probs =</span> <span class="fl">0.9</span>, <span class="at">na.rm=</span>T)) <span class="sc">|&gt;</span>  </span>
<span id="cb4-24"><a href="#cb4-24" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-25"><a href="#cb4-25" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="st">&quot;Top quartile&quot;</span> <span class="ot">=</span> <span class="fu">quantile</span>(Roll_rets ,<span class="at">probs =</span> <span class="fl">0.75</span>, <span class="at">na.rm=</span>T)) <span class="sc">|&gt;</span>  </span>
<span id="cb4-26"><a href="#cb4-26" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-27"><a href="#cb4-27" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="st">&quot;Median&quot;</span> <span class="ot">=</span> <span class="fu">quantile</span>(Roll_rets ,<span class="at">probs =</span> <span class="fl">0.5</span>, <span class="at">na.rm=</span>T)) <span class="sc">|&gt;</span>  </span>
<span id="cb4-28"><a href="#cb4-28" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-29"><a href="#cb4-29" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="st">&quot;Bottom quartile&quot;</span> <span class="ot">=</span> <span class="fu">quantile</span>(Roll_rets ,<span class="at">probs =</span> <span class="fl">0.25</span>, <span class="at">na.rm=</span>T)) <span class="sc">|&gt;</span>  </span>
<span id="cb4-30"><a href="#cb4-30" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-31"><a href="#cb4-31" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="st">&quot;Bottom decile&quot;</span> <span class="ot">=</span> <span class="fu">quantile</span>(Roll_rets ,<span class="at">probs =</span> <span class="fl">0.1</span>, <span class="at">na.rm=</span>T))  <span class="sc">|&gt;</span> </span>
<span id="cb4-32"><a href="#cb4-32" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-33"><a href="#cb4-33" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(<span class="sc">-</span> <span class="fu">c</span>(Name, Roll_rets)) <span class="sc">|&gt;</span> <span class="fu">ungroup</span>() <span class="sc">|&gt;</span> </span>
<span id="cb4-34"><a href="#cb4-34" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-35"><a href="#cb4-35" aria-hidden="true" tabindex="-1"></a>    <span class="fu">unique</span>() </span>
<span id="cb4-36"><a href="#cb4-36" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-37"><a href="#cb4-37" aria-hidden="true" tabindex="-1"></a><span class="co"># And merge it with Our Fund and Benchmark Rolling 3 year annualized returns</span></span>
<span id="cb4-38"><a href="#cb4-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-39"><a href="#cb4-39" aria-hidden="true" tabindex="-1"></a>Comparisson_df_plot <span class="ot">&lt;-</span></span>
<span id="cb4-40"><a href="#cb4-40" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-41"><a href="#cb4-41" aria-hidden="true" tabindex="-1"></a>df_rolling <span class="sc">|&gt;</span> <span class="fu">filter</span>((Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;Our_Fund&quot;</span>, <span class="st">&quot;J433&quot;</span>)) ) <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="sc">-</span>Returns) <span class="sc">|&gt;</span>  </span>
<span id="cb4-42"><a href="#cb4-42" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-43"><a href="#cb4-43" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Roll_rets&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb4-44"><a href="#cb4-44" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-45"><a href="#cb4-45" aria-hidden="true" tabindex="-1"></a>    <span class="fu">left_join</span>(ASISA_percentiles, <span class="at">by =</span> <span class="st">&quot;date&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb4-46"><a href="#cb4-46" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-47"><a href="#cb4-47" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols=</span> <span class="sc">-</span>date, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;RollRet&quot;</span>)</span>
<span id="cb4-48"><a href="#cb4-48" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-49"><a href="#cb4-49" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I graph the rolling returns of our fund versus the benchmark Capped SWIX (J433) vesus the ASISA Percentiles</span></span>
<span id="cb4-50"><a href="#cb4-50" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb4-51"><a href="#cb4-51" aria-hidden="true" tabindex="-1"></a>Comparisson_plot <span class="ot">&lt;-</span> Comparisson_df_plot <span class="sc">|&gt;</span>   </span>
<span id="cb4-52"><a href="#cb4-52" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb4-53"><a href="#cb4-53" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb4-54"><a href="#cb4-54" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-55"><a href="#cb4-55" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, RollRet, <span class="at">color =</span> Name), <span class="at">size =</span> <span class="fl">0.9</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span> </span>
<span id="cb4-56"><a href="#cb4-56" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-57"><a href="#cb4-57" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb4-58"><a href="#cb4-58" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb4-59"><a href="#cb4-59" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb4-60"><a href="#cb4-60" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb4-61"><a href="#cb4-61" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-62"><a href="#cb4-62" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb4-63"><a href="#cb4-63" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb4-64"><a href="#cb4-64" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb4-65"><a href="#cb4-65" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Benchmark (J433) vs Our Fund vs ASISA Active Funds: Rolling 3 Year Annualized Returns&quot;</span>,</span>
<span id="cb4-66"><a href="#cb4-66" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;The ASISA Active Funds are reflected by the monthly rollling 3 year annualized percentiles.&quot;</span>)</span></code></pre></div>
<pre><code>## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.</code></pre>
<div class="sourceCode" id="cb6"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches easily:</span></span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb6-3"><a href="#cb6-3" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">finplot</span>(Comparisson_plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, )</span></code></pre></div>
<p><img
src="README_files/figure-html/Rolling%203%20Year%20Annualized%20Returns-1.png" /><!-- --></p>
<h2 id="excess-returns-and-tracking-error-comparison">Excess Returns and
Tracking Error Comparison</h2>
<p>Next I annualize the returns on a 15 year basis, and plot the Excess
Returns over the benchmark (Capped SWIX) to the Tracking Error over the
past 90 months.</p>
<div class="sourceCode" id="cb7"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb7-1"><a href="#cb7-1" aria-hidden="true" tabindex="-1"></a>funds_xts <span class="ot">&lt;-</span> df_rolling <span class="sc">|&gt;</span> <span class="fu">filter</span>(<span class="sc">!</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;J433&quot;</span>))) <span class="sc">|&gt;</span> </span>
<span id="cb7-2"><a href="#cb7-2" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-3"><a href="#cb7-3" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;=</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="st">&quot;2007-10-31&quot;</span>) ) <span class="sc">|&gt;</span> <span class="co"># Filter dates to calc annualized, 15 year basis, returns below</span></span>
<span id="cb7-4"><a href="#cb7-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-5"><a href="#cb7-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(<span class="sc">-</span>Roll_rets) <span class="sc">|&gt;</span> <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Returns, <span class="at">spread_by =</span> Name)</span>
<span id="cb7-6"><a href="#cb7-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-7"><a href="#cb7-7" aria-hidden="true" tabindex="-1"></a>Benchmark_xts <span class="ot">&lt;-</span> df_rolling <span class="sc">|&gt;</span> <span class="fu">filter</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;J433&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb7-8"><a href="#cb7-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-9"><a href="#cb7-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;=</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="st">&quot;2007-10-31&quot;</span>) ) <span class="sc">|&gt;</span>  <span class="co"># Filter dates to calc annualized, 15 year basis, returns below</span></span>
<span id="cb7-10"><a href="#cb7-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-11"><a href="#cb7-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">select</span>(<span class="sc">-</span>Roll_rets) <span class="sc">|&gt;</span> </span>
<span id="cb7-12"><a href="#cb7-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-13"><a href="#cb7-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>()    </span>
<span id="cb7-14"><a href="#cb7-14" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-15"><a href="#cb7-15" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-16"><a href="#cb7-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-17"><a href="#cb7-17" aria-hidden="true" tabindex="-1"></a> </span>
<span id="cb7-18"><a href="#cb7-18" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I attempt to annualize the returns on a 15 year basis</span></span>
<span id="cb7-19"><a href="#cb7-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-20"><a href="#cb7-20" aria-hidden="true" tabindex="-1"></a>funds_ann <span class="ot">&lt;-</span> PerformanceAnalytics<span class="sc">::</span><span class="fu">Return.annualized</span>(funds_xts, <span class="at">scale =</span> <span class="dv">12</span>) <span class="sc">|&gt;</span> <span class="fu">as_tibble</span>() <span class="sc">|&gt;</span> </span>
<span id="cb7-21"><a href="#cb7-21" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-22"><a href="#cb7-22" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="dv">1</span><span class="sc">:</span><span class="dv">228</span>, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;Ret&quot;</span>)</span>
<span id="cb7-23"><a href="#cb7-23" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-24"><a href="#cb7-24" aria-hidden="true" tabindex="-1"></a>Benchmark_ann <span class="ot">&lt;-</span> PerformanceAnalytics<span class="sc">::</span><span class="fu">Return.annualized</span>(Benchmark_xts, <span class="at">scale =</span> <span class="dv">12</span>)<span class="sc">|&gt;</span> <span class="fu">as_tibble</span>() <span class="co"># = 0.0801 for future use</span></span>
<span id="cb7-25"><a href="#cb7-25" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-26"><a href="#cb7-26" aria-hidden="true" tabindex="-1"></a><span class="co"># Now to get the tracking errors</span></span>
<span id="cb7-27"><a href="#cb7-27" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-28"><a href="#cb7-28" aria-hidden="true" tabindex="-1"></a>Tracking_errors <span class="ot">&lt;-</span> PerformanceAnalytics<span class="sc">::</span><span class="fu">TrackingError</span>(funds_xts, Benchmark_xts, <span class="at">scale =</span> <span class="dv">12</span>) <span class="sc">|&gt;</span> <span class="fu">as_tibble</span>() <span class="sc">|&gt;</span> </span>
<span id="cb7-29"><a href="#cb7-29" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-30"><a href="#cb7-30" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="dv">1</span><span class="sc">:</span><span class="dv">228</span>, <span class="at">names_to =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_to =</span> <span class="st">&quot;TE&quot;</span>)</span>
<span id="cb7-31"><a href="#cb7-31" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-32"><a href="#cb7-32" aria-hidden="true" tabindex="-1"></a>Excess_returns_df <span class="ot">&lt;-</span> <span class="fu">inner_join</span>(funds_ann, Tracking_errors, <span class="at">by=</span> <span class="st">&quot;Name&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb7-33"><a href="#cb7-33" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-34"><a href="#cb7-34" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Excess_ret =</span> Ret <span class="sc">-</span> <span class="fl">0.0801</span>) <span class="sc">|&gt;</span> </span>
<span id="cb7-35"><a href="#cb7-35" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-36"><a href="#cb7-36" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Median_Ret =</span> <span class="fu">quantile</span>(Ret, <span class="at">probs=</span><span class="fl">0.5</span>, <span class="at">na.rm =</span> T)) <span class="co"># And add the median return for plot</span></span>
<span id="cb7-37"><a href="#cb7-37" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-38"><a href="#cb7-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-39"><a href="#cb7-39" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-40"><a href="#cb7-40" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-41"><a href="#cb7-41" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-42"><a href="#cb7-42" aria-hidden="true" tabindex="-1"></a><span class="co"># Lastly, I plot the results:</span></span>
<span id="cb7-43"><a href="#cb7-43" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-44"><a href="#cb7-44" aria-hidden="true" tabindex="-1"></a>Excess_returns_plot <span class="ot">&lt;-</span> Excess_returns_df <span class="sc">|&gt;</span> </span>
<span id="cb7-45"><a href="#cb7-45" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-46"><a href="#cb7-46" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span></span>
<span id="cb7-47"><a href="#cb7-47" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-48"><a href="#cb7-48" aria-hidden="true" tabindex="-1"></a>    <span class="fu">geom_point</span>(<span class="fu">aes</span>(TE, Excess_ret), <span class="at">size =</span> <span class="dv">2</span>, <span class="at">alpha=</span> <span class="fl">0.7</span>) <span class="sc">+</span> </span>
<span id="cb7-49"><a href="#cb7-49" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-50"><a href="#cb7-50" aria-hidden="true" tabindex="-1"></a>             <span class="co"># Noting that Our_Fund is at the coordinate: (0.042,0.0281)</span></span>
<span id="cb7-51"><a href="#cb7-51" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-52"><a href="#cb7-52" aria-hidden="true" tabindex="-1"></a>     <span class="fu">geom_point</span>(<span class="fu">aes</span>(<span class="at">x =</span> <span class="fl">0.042</span>, <span class="at">y =</span> <span class="fl">0.0281</span>), <span class="at">size =</span> <span class="dv">8</span>, <span class="at">color =</span><span class="st">&quot;red&quot;</span> ) <span class="sc">+</span></span>
<span id="cb7-53"><a href="#cb7-53" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-54"><a href="#cb7-54" aria-hidden="true" tabindex="-1"></a>    <span class="fu">geom_text</span>(<span class="fu">aes</span>(<span class="at">x =</span> <span class="fl">0.042</span>, <span class="at">y =</span> <span class="fl">0.0281</span>), <span class="at">label=</span><span class="st">&quot;Our Fund&quot;</span>, <span class="at">vjust=</span><span class="fl">0.5</span>, <span class="at">color =</span> <span class="st">&quot;blue&quot;</span>) <span class="sc">+</span> <span class="co"># this adds a label for the red point</span></span>
<span id="cb7-55"><a href="#cb7-55" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-56"><a href="#cb7-56" aria-hidden="true" tabindex="-1"></a>    <span class="fu">geom_vline</span>(<span class="at">xintercept=</span><span class="fl">0.0640</span>, <span class="at">linetype=</span><span class="st">&quot;dotted&quot;</span>) <span class="sc">+</span></span>
<span id="cb7-57"><a href="#cb7-57" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-58"><a href="#cb7-58" aria-hidden="true" tabindex="-1"></a>    <span class="fu">geom_text</span>(<span class="fu">aes</span>(<span class="at">x =</span> <span class="fl">0.067</span>, <span class="at">y =</span> <span class="sc">-</span><span class="fl">0.22</span>), <span class="at">label=</span><span class="st">&quot;Median&quot;</span>, <span class="at">vjust=</span><span class="dv">0</span>) <span class="sc">+</span></span>
<span id="cb7-59"><a href="#cb7-59" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-60"><a href="#cb7-60" aria-hidden="true" tabindex="-1"></a>     fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb7-61"><a href="#cb7-61" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb7-62"><a href="#cb7-62" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),  </span>
<span id="cb7-63"><a href="#cb7-63" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb7-64"><a href="#cb7-64" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb7-65"><a href="#cb7-65" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-66"><a href="#cb7-66" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb7-67"><a href="#cb7-67" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb7-68"><a href="#cb7-68" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb7-69"><a href="#cb7-69" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;Tracking Error to Capped SWIX&quot;</span>, <span class="at">y =</span> <span class="st">&quot;Ann. Return Differential vs Capped SWIX&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own. Returns are annualized, on a 90 month (15 year) basis.&quot;</span>,</span>
<span id="cb7-70"><a href="#cb7-70" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Excess Returns and Tracking Error Comparison&quot;</span>,</span>
<span id="cb7-71"><a href="#cb7-71" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;Benchmark: Capped SWIX. Tracking Error over the past 90 months&quot;</span>)</span>
<span id="cb7-72"><a href="#cb7-72" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb7-73"><a href="#cb7-73" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches easily:</span></span>
<span id="cb7-74"><a href="#cb7-74" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb7-75"><a href="#cb7-75" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">finplot</span>(Excess_returns_plot, <span class="at">x.vert =</span> F, <span class="at">x.pct =</span> T, <span class="at">y.pct =</span> T )</span></code></pre></div>
<pre><code>## Warning: Removed 5 rows containing missing values (`geom_point()`).</code></pre>
<p><img
src="README_files/figure-html/Tracking%20Errors-1.png" /><!-- --></p>
<h1 id="question-2">Question 2</h1>
<p>Economists recently pointed out that the current yield spreads in
local mid to longer dated bond yields have since 2020 been the highest
in decades.</p>
<p>Using the data/SA Bonds.rds file, I conduct an analysis of the
current yield spreads in the local bond market. In addition, I compare
the local spread to comparable international spreads, observe the
correlation between the local bond spreads and the USD-ZAR level, as
well as consider the SA 10 Year Break-Even inflation estimate.</p>
<h2 id="loading-data-1">Loading data</h2>
<div class="sourceCode" id="cb9"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb9-1"><a href="#cb9-1" aria-hidden="true" tabindex="-1"></a>SA_bonds <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/SA_Bonds.rds&quot;</span>)</span>
<span id="cb9-2"><a href="#cb9-2" aria-hidden="true" tabindex="-1"></a>BE_Infl <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/BE_Infl.rds&quot;</span>)</span>
<span id="cb9-3"><a href="#cb9-3" aria-hidden="true" tabindex="-1"></a>bonds_2y <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/bonds_2y.rds&quot;</span>)</span>
<span id="cb9-4"><a href="#cb9-4" aria-hidden="true" tabindex="-1"></a>bonds_10y <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/bonds_10y.rds&quot;</span>)</span>
<span id="cb9-5"><a href="#cb9-5" aria-hidden="true" tabindex="-1"></a>usdzar <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/usdzar.rds&quot;</span>)</span>
<span id="cb9-6"><a href="#cb9-6" aria-hidden="true" tabindex="-1"></a>ZA_Infl <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/ZA_Infl.rds&quot;</span>)</span>
<span id="cb9-7"><a href="#cb9-7" aria-hidden="true" tabindex="-1"></a>IV <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/IV.rds&quot;</span>)</span></code></pre></div>
<h2 id="global-bond-market-yields">Global Bond Market Yields</h2>
<p>Conduct data wrangling to get the 2Yr and 10Yr global yields and
their spreads in one tbl..</p>
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
<p>And finally the plot..</p>
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
<p>In analyzing the above plot, the SA Bond yield spread has been
significantly more volatile than those of Germany, China, the US, and
Japan, while having less volatility in the last few years than Brazil.
Visually confirming the notion that the current yield spreads in local
mid to longer dated bond yields have since 2020 been the highest in
decades.</p>
<h2 id="sa-bond-yields-spread-and-zarusd-exchange-rate">SA Bond Yields,
Spread, and ZAR/USD Exchange Rate</h2>
<p>I now plot the SA bond yield spreads together with the ZAR/USD
exchange rate. It is evident that the comovements between the ZAR/USD
and the SA bonds yield spread was rather strong, however, between the
periods of 2010 and 2016 this positive correlation has diminished,
likely due to the large open market asset purchases by the FED that
distorts the international spillovers.</p>
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
<span id="cb13-17"><a href="#cb13-17" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;SA Bond Yields, Spread, and ZAR/USD Exchange Rate&quot;</span>,</span>
<span id="cb13-18"><a href="#cb13-18" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb13-19"><a href="#cb13-19" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb13-20"><a href="#cb13-20" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb13-21"><a href="#cb13-21" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb13-22"><a href="#cb13-22" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(SA_Bonds_Plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> F)</span></code></pre></div>
<pre><code>## Warning: Removed 7770 rows containing missing values (`geom_line()`).</code></pre>
<p><img
src="README_files/figure-html/US%20and%20SA%20Real%20Yield%20Spread-1.png" /><!-- --></p>
<h2 id="statistics-comparison-pre-and-post-gfc">Statistics comparison
pre and post GFC</h2>
<p>Now I compare the SA vs US Bond Yields Spread statistics pre GFC vs
post GFC to see whether it has changed significantly. Overall,
considering the SA and US separately and rather loosely, the volatility
in the SA bond yield has remained rather stable comparing it pre and
post GFC.</p>
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
<h2 id="sa-break-even-inflation-yield-estimate">SA Break-Even inflation
yield estimate</h2>
<p>Next, I analyse the SA Break-Even inflation yield estimate and
compare it to the SA inflation rate. Break-even inflation is the
difference between the nominal yield on a fixed-rate investment and the
real yield (fixed spread) on an inflation-linked investment of similar
maturity and credit quality. If inflation averages more than the
break-even, the inflation-linked investment will outperform the
fixed-rate.</p>
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
<p>From the plot above, the SA average inflation rate has not surpassed
the BE inflation yield estimate since 2014, indicating that fixed
investment has been firmly preferred over the index-linked bond
investment.</p>
<h1 id="question-3">Question 3</h1>
<p>The purpose of this question is to, using the information on the ALSI
(J200) and SWIX (J400) top 40 Indexes, compare the SWIX and ALSI
methodologies by looking at the performance of different sector
exposures and stock concentrations over time.</p>
<h2 id="data-import">Data import</h2>
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
<h2 id="alsi-and-swix-weighted-portfolio-cumulative-returns">ALSI and
SWIX weighted portfolio cumulative returns</h2>
<p>I first plot the ALSI and SWIX weighted portfolio cumulative returns,
following some tedious data wrangling.</p>
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
<p>From the plot above, the cumulative weighted returns for the ALSI and
SWIX indexes are strickingly similar, however, since the onset of
COVID-19, the ALSI has achieved higher returns.</p>
<h2 id="weighted-return-contribition-of-each-sector">Weighted return
contribition of each sector</h2>
<p>I now create the final tbl that includes the weighted return
contribution of each sector to the overall weighted portfolio return
(daily). From the plots produced below, the ALSI has a over time applied
a larger weight to the Resources sector than the SWIX, where the SWIX
applies relatively larger weights to Financials and Industrials.</p>
<p>The weighted contribution per sector confirms that the additional
weight the ALSI applies to the Resources sector compared to the SWIX is
what generated its higher returns since the onset of COVID-19.</p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb24-1"><a href="#cb24-1" aria-hidden="true" tabindex="-1"></a>df_Port_ret_final <span class="ot">&lt;-</span> df_Port_ret <span class="sc">|&gt;</span> <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb24-2"><a href="#cb24-2" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-3"><a href="#cb24-3" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(date, Sector) <span class="sc">|&gt;</span>       <span class="co"># Group by Market Cap to calc each category&#39;s contr to wieghted portf return</span></span>
<span id="cb24-4"><a href="#cb24-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-5"><a href="#cb24-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wght_sector =</span> <span class="fu">coalesce</span>(J400, <span class="dv">0</span>)) <span class="sc">|&gt;</span> <span class="co"># Make NA&#39;s 0 to use PA later</span></span>
<span id="cb24-6"><a href="#cb24-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-7"><a href="#cb24-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wght_sector =</span> <span class="fu">coalesce</span>(J200, <span class="dv">0</span>)) <span class="sc">|&gt;</span></span>
<span id="cb24-8"><a href="#cb24-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-9"><a href="#cb24-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wret =</span> <span class="fu">coalesce</span>(ALSI_wret, <span class="dv">0</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb24-10"><a href="#cb24-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-11"><a href="#cb24-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wret =</span> <span class="fu">coalesce</span>(SWIX_wret, <span class="dv">0</span>)) <span class="sc">|&gt;</span></span>
<span id="cb24-12"><a href="#cb24-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-13"><a href="#cb24-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_ret_sector =</span> <span class="fu">sum</span>(ALSI_wret, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span>  <span class="co"># The weight-contribution of each sector on each day</span></span>
<span id="cb24-14"><a href="#cb24-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-15"><a href="#cb24-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_ret_sector =</span> <span class="fu">sum</span>(SWIX_wret, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span></span>
<span id="cb24-16"><a href="#cb24-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-17"><a href="#cb24-17" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">ALSI_wght_sector =</span> <span class="fu">sum</span>(J400, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span>  <span class="co"># The weighted return contribution of each sector on each day</span></span>
<span id="cb24-18"><a href="#cb24-18" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-19"><a href="#cb24-19" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SWIX_wght_sector =</span> <span class="fu">sum</span>(J200, <span class="at">na.rm =</span> T)) <span class="sc">|&gt;</span></span>
<span id="cb24-20"><a href="#cb24-20" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-21"><a href="#cb24-21" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>()</span>
<span id="cb24-22"><a href="#cb24-22" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-23"><a href="#cb24-23" aria-hidden="true" tabindex="-1"></a>ALSI_wght_sector <span class="ot">&lt;-</span>  df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,ALSI_wght_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-24"><a href="#cb24-24" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> ALSI_wght_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-25"><a href="#cb24-25" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-26"><a href="#cb24-26" aria-hidden="true" tabindex="-1"></a>SWIX_wght_sector <span class="ot">&lt;-</span> df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,SWIX_wght_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-27"><a href="#cb24-27" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> SWIX_wght_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-28"><a href="#cb24-28" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-29"><a href="#cb24-29" aria-hidden="true" tabindex="-1"></a>ALSI_ret_sector <span class="ot">&lt;-</span> df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,ALSI_ret_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-30"><a href="#cb24-30" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> ALSI_ret_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-31"><a href="#cb24-31" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-32"><a href="#cb24-32" aria-hidden="true" tabindex="-1"></a>SWIX_ret_sector <span class="ot">&lt;-</span> df_Port_ret_final <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Sector ,SWIX_ret_sector) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Sector) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> </span>
<span id="cb24-33"><a href="#cb24-33" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> SWIX_ret_sector, <span class="at">spread_by =</span> Sector)</span>
<span id="cb24-34"><a href="#cb24-34" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-35"><a href="#cb24-35" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-36"><a href="#cb24-36" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-37"><a href="#cb24-37" aria-hidden="true" tabindex="-1"></a> ALSI_RetPort_sector <span class="ot">&lt;-</span> </span>
<span id="cb24-38"><a href="#cb24-38" aria-hidden="true" tabindex="-1"></a>      rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(ALSI_ret_sector, </span>
<span id="cb24-39"><a href="#cb24-39" aria-hidden="true" tabindex="-1"></a>                                     </span>
<span id="cb24-40"><a href="#cb24-40" aria-hidden="true" tabindex="-1"></a>                       <span class="at">weights =</span> ALSI_wght_sector, <span class="at">lag_weights =</span> <span class="cn">TRUE</span>,</span>
<span id="cb24-41"><a href="#cb24-41" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-42"><a href="#cb24-42" aria-hidden="true" tabindex="-1"></a>                       <span class="at">verbose =</span> <span class="cn">TRUE</span>, <span class="at">contribution =</span> <span class="cn">TRUE</span>, </span>
<span id="cb24-43"><a href="#cb24-43" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-44"><a href="#cb24-44" aria-hidden="true" tabindex="-1"></a>                       <span class="at">value =</span> <span class="dv">1000</span>, <span class="at">geometric =</span> <span class="cn">TRUE</span>) </span>
<span id="cb24-45"><a href="#cb24-45" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-46"><a href="#cb24-46" aria-hidden="true" tabindex="-1"></a> SWIX_RetPort_sector <span class="ot">&lt;-</span> </span>
<span id="cb24-47"><a href="#cb24-47" aria-hidden="true" tabindex="-1"></a>      rmsfuns<span class="sc">::</span><span class="fu">Safe_Return.portfolio</span>(SWIX_ret_sector, </span>
<span id="cb24-48"><a href="#cb24-48" aria-hidden="true" tabindex="-1"></a>                                     </span>
<span id="cb24-49"><a href="#cb24-49" aria-hidden="true" tabindex="-1"></a>                       <span class="at">weights =</span> SWIX_wght_sector, <span class="at">lag_weights =</span> <span class="cn">TRUE</span>,</span>
<span id="cb24-50"><a href="#cb24-50" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-51"><a href="#cb24-51" aria-hidden="true" tabindex="-1"></a>                       <span class="at">verbose =</span> <span class="cn">TRUE</span>, <span class="at">contribution =</span> <span class="cn">TRUE</span>, </span>
<span id="cb24-52"><a href="#cb24-52" aria-hidden="true" tabindex="-1"></a>                       </span>
<span id="cb24-53"><a href="#cb24-53" aria-hidden="true" tabindex="-1"></a>                       <span class="at">value =</span> <span class="dv">1000</span>, <span class="at">geometric =</span> <span class="cn">TRUE</span>)</span>
<span id="cb24-54"><a href="#cb24-54" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb24-55"><a href="#cb24-55" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb24-56"><a href="#cb24-56" aria-hidden="true" tabindex="-1"></a> ALSI_RetPort_sector<span class="sc">$</span>BOP.Weight  <span class="sc">%&gt;%</span> .[<span class="fu">endpoints</span>(.,<span class="st">&#39;months&#39;</span>)] <span class="sc">%&gt;%</span> <span class="fu">chart.StackedBar</span>()</span></code></pre></div>
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
<p>Pull the rebalance days,</p>
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
<h2 id="and-10-capped-indexes-weighted-returns">6% and 10% Capped
indexes weighted returns</h2>
<p>I now analyse the impact different capping levels would have had on
both the SWIX and ALSI (6% and 10%).</p>
<p>From the ALSI capped figure, a capping of 10% has virtually the same
cumulative returns as when uncapped, with significantly lower returns
when capped at 6%. This is likely due to this 6% capping most of the
relatively higher returns generated from the Resources sector.</p>
<p>On the other hand, from the SWIX capped figure, the 10% cap would
have improved its return since the onset of COVID, likely due to a
reduction in weighted contribution from the weaker performing Industrial
and Financial sectors.</p>
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
<span id="cb38-32"><a href="#cb38-32" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Cumulative Returns of ALSI Capped at 6% and 10%&quot;</span>,</span>
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
<span id="cb38-51"><a href="#cb38-51" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Cumulative Returns of SWIX Capped at 6% and 10%&quot;</span>,</span>
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
<h1 id="question-4">Question 4</h1>
<p>For this question I using the Top 40 Index data from question 3 to
calculate the concentration of returns among the ALSI constituents
(J200) by considering it from a Principal Component Analysis (PCA)
perspective.</p>
<p>I plot the Scree plot (percentage of explained variances) and the
Cos2 plot for the constituants that explain most of the volatility
(Quality of representation).</p>
<p>Note that; A high cos2 indicates a good representation of the
variable on the principal component. Whereas a low cos2 indicates that
the variable is not perfectly represented by the PCs.</p>
<h2 id="scree-plot">Scree plot</h2>
<div class="sourceCode" id="cb42"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb42-1"><a href="#cb42-1" aria-hidden="true" tabindex="-1"></a>pacman<span class="sc">::</span><span class="fu">p_load</span>(<span class="st">&quot;RiskPortfolios&quot;</span>, <span class="st">&quot;FactoMineR&quot;</span>, <span class="st">&quot;factoextra&quot;</span>, <span class="st">&quot;broom&quot;</span>)</span>
<span id="cb42-2"><a href="#cb42-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-3"><a href="#cb42-3" aria-hidden="true" tabindex="-1"></a><span class="co">#  Extract the weighted returns for each constituent from Q3, and change to wide format to use princomp.</span></span>
<span id="cb42-4"><a href="#cb42-4" aria-hidden="true" tabindex="-1"></a><span class="co"># In addition, I transform the simple returns to log returns and mean-centre them</span></span>
<span id="cb42-5"><a href="#cb42-5" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-6"><a href="#cb42-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-7"><a href="#cb42-7" aria-hidden="true" tabindex="-1"></a>PCA_data <span class="ot">&lt;-</span> df_Port_ret <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Tickers, ALSI_wret) <span class="sc">|&gt;</span></span>
<span id="cb42-8"><a href="#cb42-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-9"><a href="#cb42-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">log_ret =</span> <span class="fu">exp</span>(ALSI_wret)<span class="sc">-</span><span class="dv">1</span>) <span class="sc">|&gt;</span>            <span class="co"># convert to log returns</span></span>
<span id="cb42-10"><a href="#cb42-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-11"><a href="#cb42-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> <span class="fu">group_by</span>(Tickers) <span class="sc">|&gt;</span> </span>
<span id="cb42-12"><a href="#cb42-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-13"><a href="#cb42-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">log_ret =</span> log_ret <span class="sc">-</span> <span class="fu">mean</span>(log_ret)) <span class="sc">|&gt;</span>   <span class="co"># Mean-centering</span></span>
<span id="cb42-14"><a href="#cb42-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-15"><a href="#cb42-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>() <span class="sc">|&gt;</span> <span class="fu">select</span>(<span class="sc">-</span>ALSI_wret) <span class="sc">|&gt;</span> </span>
<span id="cb42-16"><a href="#cb42-16" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-17"><a href="#cb42-17" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Tickers&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;log_ret&quot;</span>)</span>
<span id="cb42-18"><a href="#cb42-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-19"><a href="#cb42-19" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I source the following function from the tut:</span></span>
<span id="cb42-20"><a href="#cb42-20" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-21"><a href="#cb42-21" aria-hidden="true" tabindex="-1"></a>impute_missing_returns <span class="ot">&lt;-</span> <span class="cf">function</span>(return_mat, <span class="at">impute_returns_method =</span> <span class="st">&quot;NONE&quot;</span>, <span class="at">Seed =</span> <span class="dv">1234</span>){</span>
<span id="cb42-22"><a href="#cb42-22" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Make sure we have a date column called date:</span></span>
<span id="cb42-23"><a href="#cb42-23" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>( <span class="sc">!</span><span class="st">&quot;date&quot;</span> <span class="sc">%in%</span> <span class="fu">colnames</span>(return_mat) ) <span class="fu">stop</span>(<span class="st">&quot;No &#39;date&#39; column provided in return_mat. Try again please.&quot;</span>)</span>
<span id="cb42-24"><a href="#cb42-24" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-25"><a href="#cb42-25" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Note my use of &#39;any&#39; below...</span></span>
<span id="cb42-26"><a href="#cb42-26" aria-hidden="true" tabindex="-1"></a>  <span class="co"># Also note that I &#39;return&#39; return_mat - which stops the function and returns return_mat. </span></span>
<span id="cb42-27"><a href="#cb42-27" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>( impute_returns_method <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;NONE&quot;</span>, <span class="st">&quot;None&quot;</span>, <span class="st">&quot;none&quot;</span>) ) {</span>
<span id="cb42-28"><a href="#cb42-28" aria-hidden="true" tabindex="-1"></a>    <span class="cf">if</span>( <span class="fu">any</span>(<span class="fu">is.na</span>(return_mat)) ) <span class="fu">warning</span>(<span class="st">&quot;There are missing values in the return matrix.. Consider maybe using impute_returns_method = &#39;Drawn_Distribution_Own&#39; / &#39;Drawn_Distribution_Collective&#39;&quot;</span>)</span>
<span id="cb42-29"><a href="#cb42-29" aria-hidden="true" tabindex="-1"></a>    <span class="fu">return</span>(return_mat)</span>
<span id="cb42-30"><a href="#cb42-30" aria-hidden="true" tabindex="-1"></a>  }</span>
<span id="cb42-31"><a href="#cb42-31" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-32"><a href="#cb42-32" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb42-33"><a href="#cb42-33" aria-hidden="true" tabindex="-1"></a>  <span class="cf">if</span>( impute_returns_method  <span class="sc">==</span> <span class="st">&quot;Average&quot;</span>) {</span>
<span id="cb42-34"><a href="#cb42-34" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-35"><a href="#cb42-35" aria-hidden="true" tabindex="-1"></a>    return_mat <span class="ot">&lt;-</span></span>
<span id="cb42-36"><a href="#cb42-36" aria-hidden="true" tabindex="-1"></a>      return_mat <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Stocks, Returns, <span class="sc">-</span>date) <span class="sc">%&gt;%</span></span>
<span id="cb42-37"><a href="#cb42-37" aria-hidden="true" tabindex="-1"></a>      <span class="fu">group_by</span>(date) <span class="sc">%&gt;%</span></span>
<span id="cb42-38"><a href="#cb42-38" aria-hidden="true" tabindex="-1"></a>      <span class="fu">mutate</span>(<span class="at">Avg =</span> <span class="fu">mean</span>(Returns, <span class="at">na.rm=</span>T)) <span class="sc">%&gt;%</span></span>
<span id="cb42-39"><a href="#cb42-39" aria-hidden="true" tabindex="-1"></a>      <span class="fu">mutate</span>(<span class="at">Avg =</span> <span class="fu">coalesce</span>(Avg, <span class="dv">0</span>)) <span class="sc">%&gt;%</span> <span class="co"># date with no returns - set avg to zero</span></span>
<span id="cb42-40"><a href="#cb42-40" aria-hidden="true" tabindex="-1"></a>      <span class="fu">ungroup</span>() <span class="sc">%&gt;%</span></span>
<span id="cb42-41"><a href="#cb42-41" aria-hidden="true" tabindex="-1"></a>      <span class="fu">mutate</span>(<span class="at">Returns =</span> <span class="fu">coalesce</span>(Returns, Avg)) <span class="sc">%&gt;%</span> <span class="fu">select</span>(<span class="sc">-</span>Avg) <span class="sc">%&gt;%</span> <span class="fu">spread</span>(Stocks, Returns)</span>
<span id="cb42-42"><a href="#cb42-42" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-43"><a href="#cb42-43" aria-hidden="true" tabindex="-1"></a>    <span class="co"># That is just so much easier when tidy right? See how I gathered and spread again to give back a wide df?</span></span>
<span id="cb42-44"><a href="#cb42-44" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-45"><a href="#cb42-45" aria-hidden="true" tabindex="-1"></a>  } <span class="cf">else</span></span>
<span id="cb42-46"><a href="#cb42-46" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-47"><a href="#cb42-47" aria-hidden="true" tabindex="-1"></a>    <span class="cf">if</span>( impute_returns_method  <span class="sc">==</span> <span class="st">&quot;Drawn_Distribution_Own&quot;</span>) {</span>
<span id="cb42-48"><a href="#cb42-48" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-49"><a href="#cb42-49" aria-hidden="true" tabindex="-1"></a>      <span class="fu">set.seed</span>(Seed)</span>
<span id="cb42-50"><a href="#cb42-50" aria-hidden="true" tabindex="-1"></a>      N <span class="ot">&lt;-</span> <span class="fu">nrow</span>(return_mat)</span>
<span id="cb42-51"><a href="#cb42-51" aria-hidden="true" tabindex="-1"></a>      return_mat <span class="ot">&lt;-</span></span>
<span id="cb42-52"><a href="#cb42-52" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-53"><a href="#cb42-53" aria-hidden="true" tabindex="-1"></a>        <span class="fu">left_join</span>(return_mat <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Stocks, Returns, <span class="sc">-</span>date),</span>
<span id="cb42-54"><a href="#cb42-54" aria-hidden="true" tabindex="-1"></a>                  return_mat <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Stocks, Returns, <span class="sc">-</span>date) <span class="sc">%&gt;%</span> <span class="fu">group_by</span>(Stocks) <span class="sc">%&gt;%</span></span>
<span id="cb42-55"><a href="#cb42-55" aria-hidden="true" tabindex="-1"></a>                    <span class="fu">do</span>(<span class="at">Dens =</span> <span class="fu">density</span>(.<span class="sc">$</span>Returns, <span class="at">na.rm=</span>T)) <span class="sc">%&gt;%</span></span>
<span id="cb42-56"><a href="#cb42-56" aria-hidden="true" tabindex="-1"></a>                    <span class="fu">ungroup</span>() <span class="sc">%&gt;%</span> <span class="fu">group_by</span>(Stocks) <span class="sc">%&gt;%</span> <span class="co"># done to avoid warning.</span></span>
<span id="cb42-57"><a href="#cb42-57" aria-hidden="true" tabindex="-1"></a>                    <span class="fu">do</span>(<span class="at">Random_Draws =</span> <span class="fu">sample</span>(.<span class="sc">$</span>Dens[[<span class="dv">1</span>]]<span class="sc">$</span>x, N, <span class="at">replace =</span> <span class="cn">TRUE</span>, <span class="at">prob=</span>.<span class="sc">$</span>Dens[[<span class="dv">1</span>]]<span class="sc">$</span>y)),</span>
<span id="cb42-58"><a href="#cb42-58" aria-hidden="true" tabindex="-1"></a>                  <span class="at">by =</span> <span class="st">&quot;Stocks&quot;</span></span>
<span id="cb42-59"><a href="#cb42-59" aria-hidden="true" tabindex="-1"></a>        ) <span class="sc">%&gt;%</span>  <span class="fu">group_by</span>(Stocks) <span class="sc">%&gt;%</span> <span class="fu">mutate</span>(<span class="at">Row =</span> <span class="fu">row_number</span>()) <span class="sc">%&gt;%</span> <span class="fu">mutate</span>(<span class="at">Returns =</span> <span class="fu">coalesce</span>(Returns, Random_Draws[[<span class="dv">1</span>]][Row])) <span class="sc">%&gt;%</span></span>
<span id="cb42-60"><a href="#cb42-60" aria-hidden="true" tabindex="-1"></a>        <span class="fu">select</span>(<span class="sc">-</span>Random_Draws, <span class="sc">-</span>Row) <span class="sc">%&gt;%</span> <span class="fu">ungroup</span>() <span class="sc">%&gt;%</span> <span class="fu">spread</span>(Stocks, Returns)</span>
<span id="cb42-61"><a href="#cb42-61" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-62"><a href="#cb42-62" aria-hidden="true" tabindex="-1"></a>    } <span class="cf">else</span></span>
<span id="cb42-63"><a href="#cb42-63" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-64"><a href="#cb42-64" aria-hidden="true" tabindex="-1"></a>      <span class="cf">if</span>( impute_returns_method  <span class="sc">==</span> <span class="st">&quot;Drawn_Distribution_Collective&quot;</span>) {</span>
<span id="cb42-65"><a href="#cb42-65" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-66"><a href="#cb42-66" aria-hidden="true" tabindex="-1"></a>        <span class="fu">set.seed</span>(Seed)</span>
<span id="cb42-67"><a href="#cb42-67" aria-hidden="true" tabindex="-1"></a>        NAll <span class="ot">&lt;-</span> <span class="fu">nrow</span>(return_mat <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Stocks, Returns, <span class="sc">-</span>date))</span>
<span id="cb42-68"><a href="#cb42-68" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-69"><a href="#cb42-69" aria-hidden="true" tabindex="-1"></a>        return_mat <span class="ot">&lt;-</span></span>
<span id="cb42-70"><a href="#cb42-70" aria-hidden="true" tabindex="-1"></a>          <span class="fu">bind_cols</span>(</span>
<span id="cb42-71"><a href="#cb42-71" aria-hidden="true" tabindex="-1"></a>          return_mat <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Stocks, Returns, <span class="sc">-</span>date),</span>
<span id="cb42-72"><a href="#cb42-72" aria-hidden="true" tabindex="-1"></a>          return_mat <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Stocks, Returns, <span class="sc">-</span>date) <span class="sc">%&gt;%</span></span>
<span id="cb42-73"><a href="#cb42-73" aria-hidden="true" tabindex="-1"></a>            <span class="fu">do</span>(<span class="at">Dens =</span> <span class="fu">density</span>(.<span class="sc">$</span>Returns, <span class="at">na.rm=</span>T)) <span class="sc">%&gt;%</span></span>
<span id="cb42-74"><a href="#cb42-74" aria-hidden="true" tabindex="-1"></a>            <span class="fu">do</span>(<span class="at">Random_Draws =</span> <span class="fu">sample</span>(.<span class="sc">$</span>Dens[[<span class="dv">1</span>]]<span class="sc">$</span>x, NAll, <span class="at">replace =</span> <span class="cn">TRUE</span>, <span class="at">prob=</span>.<span class="sc">$</span>Dens[[<span class="dv">1</span>]]<span class="sc">$</span>y)) <span class="sc">%&gt;%</span> <span class="fu">unnest</span>(Random_Draws)</span>
<span id="cb42-75"><a href="#cb42-75" aria-hidden="true" tabindex="-1"></a>          ) <span class="sc">%&gt;%</span></span>
<span id="cb42-76"><a href="#cb42-76" aria-hidden="true" tabindex="-1"></a>          <span class="fu">mutate</span>(<span class="at">Returns =</span> <span class="fu">coalesce</span>(Returns, Random_Draws)) <span class="sc">%&gt;%</span> <span class="fu">select</span>(<span class="sc">-</span>Random_Draws) <span class="sc">%&gt;%</span> <span class="fu">spread</span>(Stocks, Returns)</span>
<span id="cb42-77"><a href="#cb42-77" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-78"><a href="#cb42-78" aria-hidden="true" tabindex="-1"></a>      } <span class="cf">else</span></span>
<span id="cb42-79"><a href="#cb42-79" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-80"><a href="#cb42-80" aria-hidden="true" tabindex="-1"></a>        <span class="cf">if</span>( impute_returns_method  <span class="sc">==</span> <span class="st">&quot;Zero&quot;</span>) {</span>
<span id="cb42-81"><a href="#cb42-81" aria-hidden="true" tabindex="-1"></a>        <span class="fu">warning</span>(<span class="st">&quot;This is probably not the best idea but who am I to judge....&quot;</span>)</span>
<span id="cb42-82"><a href="#cb42-82" aria-hidden="true" tabindex="-1"></a>          return_mat[<span class="fu">is.na</span>(return_mat)] <span class="ot">&lt;-</span> <span class="dv">0</span></span>
<span id="cb42-83"><a href="#cb42-83" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-84"><a href="#cb42-84" aria-hidden="true" tabindex="-1"></a>        } <span class="cf">else</span></span>
<span id="cb42-85"><a href="#cb42-85" aria-hidden="true" tabindex="-1"></a>          <span class="fu">stop</span>(<span class="st">&quot;Please provide a valid impute_returns_method method. Options include:</span><span class="sc">\n</span><span class="st">&#39;Average&#39;, &#39;Drawn_Distribution_Own&#39;, &#39;Drawn_Distribution_Collective&#39; and &#39;Zero&#39;.&quot;</span>)</span>
<span id="cb42-86"><a href="#cb42-86" aria-hidden="true" tabindex="-1"></a>}</span>
<span id="cb42-87"><a href="#cb42-87" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-88"><a href="#cb42-88" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-89"><a href="#cb42-89" aria-hidden="true" tabindex="-1"></a><span class="fu">options</span>(<span class="at">scipen=</span><span class="dv">999</span>) <span class="co"># To remove scientific notation</span></span>
<span id="cb42-90"><a href="#cb42-90" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-91"><a href="#cb42-91" aria-hidden="true" tabindex="-1"></a>return_matrix <span class="ot">&lt;-</span> </span>
<span id="cb42-92"><a href="#cb42-92" aria-hidden="true" tabindex="-1"></a>  <span class="fu">impute_missing_returns</span>(PCA_data, <span class="at">impute_returns_method =</span> <span class="st">&quot;Drawn_Distribution_Collective&quot;</span>, <span class="at">Seed =</span> <span class="fu">as.numeric</span>(<span class="fu">format</span>( <span class="fu">Sys.time</span>(), <span class="st">&quot;%Y%d%H%M&quot;</span>)))</span>
<span id="cb42-93"><a href="#cb42-93" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-94"><a href="#cb42-94" aria-hidden="true" tabindex="-1"></a><span class="co"># Remove the date column</span></span>
<span id="cb42-95"><a href="#cb42-95" aria-hidden="true" tabindex="-1"></a>return_matrix_Nodate <span class="ot">&lt;-</span> <span class="fu">data.matrix</span>(return_matrix[, <span class="sc">-</span><span class="dv">1</span>])</span>
<span id="cb42-96"><a href="#cb42-96" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-97"><a href="#cb42-97" aria-hidden="true" tabindex="-1"></a><span class="co"># Simple Sample covariance and mean:</span></span>
<span id="cb42-98"><a href="#cb42-98" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-99"><a href="#cb42-99" aria-hidden="true" tabindex="-1"></a>Sigma <span class="ot">&lt;-</span> RiskPortfolios<span class="sc">::</span><span class="fu">covEstimation</span>(return_matrix_Nodate)</span>
<span id="cb42-100"><a href="#cb42-100" aria-hidden="true" tabindex="-1"></a>Mu <span class="ot">&lt;-</span> RiskPortfolios<span class="sc">::</span><span class="fu">meanEstimation</span>(return_matrix_Nodate)</span>
<span id="cb42-101"><a href="#cb42-101" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb42-102"><a href="#cb42-102" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-103"><a href="#cb42-103" aria-hidden="true" tabindex="-1"></a><span class="co"># Conduct PCA:</span></span>
<span id="cb42-104"><a href="#cb42-104" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-105"><a href="#cb42-105" aria-hidden="true" tabindex="-1"></a>pca <span class="ot">&lt;-</span> <span class="fu">prcomp</span>(return_matrix_Nodate)   </span>
<span id="cb42-106"><a href="#cb42-106" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb42-107"><a href="#cb42-107" aria-hidden="true" tabindex="-1"></a><span class="co"># And now for plotting</span></span></code></pre></div>
<div class="sourceCode" id="cb43"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb43-1"><a href="#cb43-1" aria-hidden="true" tabindex="-1"></a><span class="fu">fviz_screeplot</span>(pca, <span class="at">ncp=</span><span class="dv">10</span>)</span></code></pre></div>
<p><img src="README_files/figure-html/scree%20plot-1.png" /><!-- --></p>
<p>The plot above shows: nearly 20% of variation in the ALSI index is
explained by a single component. It does not give insight into what this
component or factor might be, but it tells us how this component is
calculated linearly. In addition, excluding the large portion explained
by the first two PC’s, the remaining seven PC’s accounts for roughly the
same amount of variation in the ALSI.</p>
<h2 id="cos2-plot">Cos2 plot</h2>
<p>Given the large contribution of the first two components, I generate
the cos2 plot below for the first two PC’s. NPN, BHP, and AGL have high
cos2 values, indicating a good representation of the variable on the
first two PC’s.</p>
<p>In conclusion; NPN, BHP, and AGL constituents explain a significant
portion of the variation in the ALSI Top 40 index.</p>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb44-1"><a href="#cb44-1" aria-hidden="true" tabindex="-1"></a><span class="fu">fviz_cos2</span>(pca, <span class="at">choice =</span> <span class="st">&quot;var&quot;</span>, <span class="at">axes =</span> <span class="dv">1</span><span class="sc">:</span><span class="dv">2</span>, <span class="at">top =</span> <span class="dv">10</span>)</span></code></pre></div>
<p><img src="README_files/figure-html/cos2%20plot-1.png" /><!-- --></p>
<h1 id="question-5">Question 5</h1>
<p>The purpose of this question is twofold; To comment on the following
two statements:</p>
<ul>
<li>The South African rand (ZAR) has over the past few years been one of
the most volatile currencies;</li>
<li>The ZAR has generally performed well during periods where G10
currency carry trades have been favourable and these currency valuations
relatively cheap. Globally, it has been one of the currencies that most
benefit during periods where the Dollar is comparatively strong,
indicating a risk-on sentiment.</li>
</ul>
<h2 id="loading-data-2">Loading data</h2>
<div class="sourceCode" id="cb45"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb45-1"><a href="#cb45-1" aria-hidden="true" tabindex="-1"></a>cncy <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/currencies.rds&quot;</span>)</span>
<span id="cb45-2"><a href="#cb45-2" aria-hidden="true" tabindex="-1"></a>cncy_Carry <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/cncy_Carry.rds&quot;</span>)</span>
<span id="cb45-3"><a href="#cb45-3" aria-hidden="true" tabindex="-1"></a>cncy_value <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/cncy_value.rds&quot;</span>)</span>
<span id="cb45-4"><a href="#cb45-4" aria-hidden="true" tabindex="-1"></a>cncyIV <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/cncyIV.rds&quot;</span>)</span>
<span id="cb45-5"><a href="#cb45-5" aria-hidden="true" tabindex="-1"></a>bbdxy <span class="ot">&lt;-</span> <span class="fu">read_rds</span>(<span class="st">&quot;data/bbdxy.rds&quot;</span>)</span>
<span id="cb45-6"><a href="#cb45-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb45-7"><a href="#cb45-7" aria-hidden="true" tabindex="-1"></a>Currency_names_full <span class="ot">&lt;-</span> cncy <span class="sc">|&gt;</span> <span class="fu">summarise</span>(Name) <span class="sc">|&gt;</span> <span class="fu">unique</span>() <span class="sc">|&gt;</span> <span class="fu">pull</span>() </span>
<span id="cb45-8"><a href="#cb45-8" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb45-9"><a href="#cb45-9" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb45-10"><a href="#cb45-10" aria-hidden="true" tabindex="-1"></a>pacman<span class="sc">::</span><span class="fu">p_load</span>(<span class="st">&quot;MTS&quot;</span>, <span class="st">&quot;robustbase&quot;</span>,<span class="st">&quot;fGarch&quot;</span>)</span>
<span id="cb45-11"><a href="#cb45-11" aria-hidden="true" tabindex="-1"></a>pacman<span class="sc">::</span><span class="fu">p_load</span>(<span class="st">&quot;tidyverse&quot;</span>, <span class="st">&quot;devtools&quot;</span>, <span class="st">&quot;rugarch&quot;</span>, <span class="st">&quot;rmgarch&quot;</span>, </span>
<span id="cb45-12"><a href="#cb45-12" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;forecast&quot;</span>, <span class="st">&quot;tbl2xts&quot;</span>, <span class="st">&quot;lubridate&quot;</span>, <span class="st">&quot;PerformanceAnalytics&quot;</span>, </span>
<span id="cb45-13"><a href="#cb45-13" aria-hidden="true" tabindex="-1"></a>    <span class="st">&quot;ggthemes&quot;</span>, <span class="st">&quot;MTS&quot;</span>)</span>
<span id="cb45-14"><a href="#cb45-14" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb45-15"><a href="#cb45-15" aria-hidden="true" tabindex="-1"></a><span class="fu">print</span>(Currency_names_full)</span></code></pre></div>
<pre><code>##  [1] &quot;Argentina_Cncy&quot;     &quot;Australia_Cncy_Inv&quot; &quot;Bostwana_Cncy_Inv&quot; 
##  [4] &quot;Brazil_Cncy&quot;        &quot;Bulgaria_Cncy&quot;      &quot;Canada_Cncy&quot;       
##  [7] &quot;Chile_Cncy&quot;         &quot;China_Cncy&quot;         &quot;Columbia_Cncy&quot;     
## [10] &quot;Czech_Cncy&quot;         &quot;Denmark_Cncy&quot;       &quot;Egypt_Cncy&quot;        
## [13] &quot;EU_Cncy_Inv&quot;        &quot;Ghana_Cncy&quot;         &quot;HongKong_Cncy&quot;     
## [16] &quot;Hungary_Cncy&quot;       &quot;India_Cncy&quot;         &quot;Israel_Cncy&quot;       
## [19] &quot;Japan_Cncy&quot;         &quot;Malaysia_Cncy&quot;      &quot;Mexico_Cncy&quot;       
## [22] &quot;Nigeria_Cncy&quot;       &quot;Norway_Cncy&quot;        &quot;NZ_Cncy_Inv&quot;       
## [25] &quot;Peru_Cncy&quot;          &quot;Philipines_Cncy&quot;    &quot;Poland_Cncy&quot;       
## [28] &quot;Romania_Cncy&quot;       &quot;Russia_Cncy&quot;        &quot;Saudi_Cncy&quot;        
## [31] &quot;Singapore_Cncy&quot;     &quot;SouthAfrica_Cncy&quot;   &quot;SouthKorea_Cncy&quot;   
## [34] &quot;Sweden_Cncy&quot;        &quot;Taiwan_Cncy&quot;        &quot;Thailand_Cncy&quot;     
## [37] &quot;Turkey_Cncy&quot;        &quot;UAE_Cncy&quot;           &quot;Uganda_Cncy&quot;       
## [40] &quot;UK_Cncy_Inv&quot;        &quot;Zambia_Cncy&quot;</code></pre>
<h2 id="sample-sd-comparison-of-selected-currencies">Sample SD
comparison of selected currencies</h2>
<p>Lets investigate the first point to comment on : “The South African
rand (ZAR) has over the past few years been one of the most volatile
currencies”. To investigate this, I select 8 countries’ currencies:
Brazil, EU, India, SA, Turkey, Poland, Zambia, and the UK (The
justification of these choices will be discussed in the final report),
and compare their respective dollar exchange rates.</p>
<div class="sourceCode" id="cb47"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb47-1"><a href="#cb47-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Remove the common second part of names</span></span>
<span id="cb47-2"><a href="#cb47-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-3"><a href="#cb47-3" aria-hidden="true" tabindex="-1"></a>Curr_df <span class="ot">&lt;-</span> cncy <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="at">Name =</span> <span class="fu">gsub</span>( <span class="st">&quot;_Cncy&quot;</span>, <span class="st">&quot;&quot;</span>, Name)) <span class="sc">|&gt;</span> <span class="fu">mutate</span>(<span class="at">Name =</span> <span class="fu">gsub</span>( <span class="st">&quot;_Inv&quot;</span>, <span class="st">&quot;&quot;</span>, Name)) </span>
<span id="cb47-4"><a href="#cb47-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-5"><a href="#cb47-5" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-6"><a href="#cb47-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-7"><a href="#cb47-7" aria-hidden="true" tabindex="-1"></a>Countries_to_consider <span class="ot">&lt;-</span> <span class="fu">c</span>(<span class="st">&quot;Brazil&quot;</span>,<span class="st">&quot;EU&quot;</span>,<span class="st">&quot;India&quot;</span>,<span class="st">&quot;Poland&quot;</span>,<span class="st">&quot;Zambia&quot;</span>,<span class="st">&quot;Turkey&quot;</span>,<span class="st">&quot;SouthAfrica&quot;</span>,<span class="st">&quot;UK&quot;</span>)</span>
<span id="cb47-8"><a href="#cb47-8" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-9"><a href="#cb47-9" aria-hidden="true" tabindex="-1"></a><span class="co"># filter countries to consider</span></span>
<span id="cb47-10"><a href="#cb47-10" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-11"><a href="#cb47-11" aria-hidden="true" tabindex="-1"></a>Curr_df <span class="ot">&lt;-</span> Curr_df <span class="sc">|&gt;</span> <span class="fu">filter</span>(Name <span class="sc">%in%</span> Countries_to_consider) </span></code></pre></div>
<p>To get an initial comparison of volatility of our selected
currencies, I plot the sample standard deviation in log growth against
the dollar. I also filter the dates to consider the period from 2006
onwards to remove extremely volatile periods for international
currencies.</p>
<div class="sourceCode" id="cb48"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb48-1"><a href="#cb48-1" aria-hidden="true" tabindex="-1"></a>SD_plot_df <span class="ot">&lt;-</span> Curr_df <span class="sc">|&gt;</span>  <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb48-2"><a href="#cb48-2" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-3"><a href="#cb48-3" aria-hidden="true" tabindex="-1"></a>    <span class="fu">group_by</span>(Name) <span class="sc">|&gt;</span> </span>
<span id="cb48-4"><a href="#cb48-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-5"><a href="#cb48-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Growth =</span> <span class="fu">log</span>(Price) <span class="sc">-</span> <span class="fu">lag</span>(<span class="fu">log</span>(Price))) <span class="sc">|&gt;</span> </span>
<span id="cb48-6"><a href="#cb48-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-7"><a href="#cb48-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;</span> dplyr<span class="sc">::</span><span class="fu">first</span>(date)) <span class="sc">|&gt;</span>  </span>
<span id="cb48-8"><a href="#cb48-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-9"><a href="#cb48-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">scaledgrowth =</span> Growth <span class="sc">-</span> <span class="fu">mean</span>(Growth, <span class="at">rm.na =</span> T)) <span class="sc">|&gt;</span>     <span class="co"># Scale the Growth by demeaning</span></span>
<span id="cb48-10"><a href="#cb48-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-11"><a href="#cb48-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SampleSD =</span> (<span class="fu">sqrt</span>(scaledgrowth<span class="sc">^</span><span class="dv">2</span>))) <span class="sc">|&gt;</span> </span>
<span id="cb48-12"><a href="#cb48-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-13"><a href="#cb48-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>() <span class="sc">|&gt;</span> </span>
<span id="cb48-14"><a href="#cb48-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-15"><a href="#cb48-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="dv">20041231</span>))</span>
<span id="cb48-16"><a href="#cb48-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-17"><a href="#cb48-17" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-18"><a href="#cb48-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-19"><a href="#cb48-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-20"><a href="#cb48-20" aria-hidden="true" tabindex="-1"></a>Scaledgrowth_plot_df <span class="ot">&lt;-</span>  SD_plot_df <span class="sc">|&gt;</span> </span>
<span id="cb48-21"><a href="#cb48-21" aria-hidden="true" tabindex="-1"></a> </span>
<span id="cb48-22"><a href="#cb48-22" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb48-23"><a href="#cb48-23" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb48-24"><a href="#cb48-24" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, scaledgrowth , <span class="at">color =</span> Name), <span class="at">size =</span> <span class="fl">0.6</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb48-25"><a href="#cb48-25" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-26"><a href="#cb48-26" aria-hidden="true" tabindex="-1"></a>    <span class="fu">facet_wrap</span>(<span class="sc">~</span>Name, <span class="at">scales =</span> <span class="st">&quot;free_y&quot;</span>)<span class="sc">+</span></span>
<span id="cb48-27"><a href="#cb48-27" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb48-28"><a href="#cb48-28" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb48-29"><a href="#cb48-29" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb48-30"><a href="#cb48-30" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb48-31"><a href="#cb48-31" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb48-32"><a href="#cb48-32" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-33"><a href="#cb48-33" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb48-34"><a href="#cb48-34" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb48-35"><a href="#cb48-35" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb48-36"><a href="#cb48-36" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Scaled (demeaned) Log Growth of Respective Currencies to USD since 2005.&quot;</span>,</span>
<span id="cb48-37"><a href="#cb48-37" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb48-38"><a href="#cb48-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-39"><a href="#cb48-39" aria-hidden="true" tabindex="-1"></a>SD_plot <span class="ot">&lt;-</span> SD_plot_df <span class="sc">|&gt;</span>  </span>
<span id="cb48-40"><a href="#cb48-40" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-41"><a href="#cb48-41" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb48-42"><a href="#cb48-42" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb48-43"><a href="#cb48-43" aria-hidden="true" tabindex="-1"></a>   <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, SampleSD , <span class="at">color =</span> Name), <span class="at">size =</span> <span class="fl">0.6</span>, <span class="at">alpha =</span> <span class="fl">0.7</span>) <span class="sc">+</span></span>
<span id="cb48-44"><a href="#cb48-44" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-45"><a href="#cb48-45" aria-hidden="true" tabindex="-1"></a>    <span class="fu">facet_wrap</span>(<span class="sc">~</span>Name, <span class="at">scales =</span> <span class="st">&quot;free_y&quot;</span>)<span class="sc">+</span></span>
<span id="cb48-46"><a href="#cb48-46" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb48-47"><a href="#cb48-47" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb48-48"><a href="#cb48-48" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb48-49"><a href="#cb48-49" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb48-50"><a href="#cb48-50" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb48-51"><a href="#cb48-51" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb48-52"><a href="#cb48-52" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb48-53"><a href="#cb48-53" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb48-54"><a href="#cb48-54" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;%&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb48-55"><a href="#cb48-55" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;Sample Standard Deviation of Respective Currencies to USD since 2005.&quot;</span>,</span>
<span id="cb48-56"><a href="#cb48-56" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;&quot;</span>)</span>
<span id="cb48-57"><a href="#cb48-57" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-58"><a href="#cb48-58" aria-hidden="true" tabindex="-1"></a><span class="co"># Finplot for finishing touches:</span></span>
<span id="cb48-59"><a href="#cb48-59" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-60"><a href="#cb48-60" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-61"><a href="#cb48-61" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb48-62"><a href="#cb48-62" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(Scaledgrowth_plot_df, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> T)</span></code></pre></div>
<pre><code>## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.</code></pre>
<p><img
src="README_files/figure-html/unnamed-chunk-4-1.png" /><!-- --></p>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb50-1"><a href="#cb50-1" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(SD_plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> T)</span></code></pre></div>
<pre><code>## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.</code></pre>
<p><img
src="README_files/figure-html/unnamed-chunk-4-2.png" /><!-- --></p>
<p>From this initial inspection of sample SD, the plot indicates that
the ZAR has recently not been the most volatile WITH RESPECT TO THE USD.
However, lets take a deeper dive into the volatility of these currencies
and try and fit a multivariate GARCH model, but by also including the
Bloomberg Dollar Spot Index (BBDXY) as a variable; it tracks the
performance of a basket of 10 leading global currencies versus the U.S.
Dollar. It has a dynamically updated composition and represents a
diverse set of currencies that are important from trade and liquidity
perspectives.</p>
<p>Importantly, since the UK and a large part of the EU is almost always
included in the BBDXY as the top 10 leading currencies, I remove them
from the model as their effect against the dollar is endogenous. From
this multivariate GARCH, I can estimate the conditional time-varying
correlation the ZAR has versus the other currencies, as well as see
whether the ZAR is one of the currencies that benefits the most during
periods where the Dollar is comparatively strong, indicating a risk-on
sentiment.</p>
<h2 id="mv-conditional-heteroskedasticity-tests">MV Conditional
Heteroskedasticity tests</h2>
<p>I conduct MV Portmanteau tests using MarchTest from MTS package,
after I join the BBDXY data and drop the UK and EU as discussed
above.</p>
<div class="sourceCode" id="cb52"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb52-1"><a href="#cb52-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Calculate the scaled (demeaned) log growth for BBDXY </span></span>
<span id="cb52-2"><a href="#cb52-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-3"><a href="#cb52-3" aria-hidden="true" tabindex="-1"></a>ln_bbdxy <span class="ot">&lt;-</span> bbdxy <span class="sc">|&gt;</span> <span class="fu">arrange</span>(date) <span class="sc">|&gt;</span> </span>
<span id="cb52-4"><a href="#cb52-4" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-5"><a href="#cb52-5" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Growth =</span> <span class="fu">log</span>(Price) <span class="sc">-</span> <span class="fu">lag</span>(<span class="fu">log</span>(Price))) <span class="sc">|&gt;</span> </span>
<span id="cb52-6"><a href="#cb52-6" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-7"><a href="#cb52-7" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;</span> dplyr<span class="sc">::</span><span class="fu">first</span>(date)) <span class="sc">|&gt;</span>  </span>
<span id="cb52-8"><a href="#cb52-8" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-9"><a href="#cb52-9" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">scaledgrowth =</span> Growth <span class="sc">-</span> <span class="fu">mean</span>(Growth, <span class="at">rm.na =</span> T)) <span class="sc">|&gt;</span>     <span class="co"># Scale the Growth by demeaning</span></span>
<span id="cb52-10"><a href="#cb52-10" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-11"><a href="#cb52-11" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">SampleSD =</span> (<span class="fu">sqrt</span>(scaledgrowth<span class="sc">^</span><span class="dv">2</span>))) <span class="sc">|&gt;</span> </span>
<span id="cb52-12"><a href="#cb52-12" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-13"><a href="#cb52-13" aria-hidden="true" tabindex="-1"></a>    <span class="fu">ungroup</span>() <span class="sc">|&gt;</span> </span>
<span id="cb52-14"><a href="#cb52-14" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-15"><a href="#cb52-15" aria-hidden="true" tabindex="-1"></a>    <span class="fu">filter</span>(date <span class="sc">&gt;</span> lubridate<span class="sc">::</span><span class="fu">ymd</span>(<span class="dv">20041231</span>))</span>
<span id="cb52-16"><a href="#cb52-16" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-17"><a href="#cb52-17" aria-hidden="true" tabindex="-1"></a><span class="co"># Now I merge it with the rest, and remove the UK, EU, and Zambia</span></span>
<span id="cb52-18"><a href="#cb52-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-19"><a href="#cb52-19" aria-hidden="true" tabindex="-1"></a>gwt <span class="ot">&lt;-</span> SD_plot_df <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Name, Growth) <span class="sc">|&gt;</span> <span class="fu">filter</span>(<span class="sc">!</span>(Name <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;EU&quot;</span>, <span class="st">&quot;UK&quot;</span>, <span class="st">&quot;Zambia&quot;</span>))) <span class="sc">|&gt;</span> </span>
<span id="cb52-20"><a href="#cb52-20" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-21"><a href="#cb52-21" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_wider</span>(<span class="at">names_from =</span> <span class="st">&quot;Name&quot;</span>, <span class="at">values_from =</span> <span class="st">&quot;Growth&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb52-22"><a href="#cb52-22" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-23"><a href="#cb52-23" aria-hidden="true" tabindex="-1"></a>    <span class="fu">left_join</span>(ln_bbdxy <span class="sc">|&gt;</span> <span class="fu">select</span>(date, Growth) <span class="sc">|&gt;</span> <span class="fu">rename</span>(<span class="at">BBDXY =</span> Growth), <span class="at">by=</span> <span class="fu">c</span>(<span class="st">&quot;date&quot;</span>)) <span class="sc">|&gt;</span> </span>
<span id="cb52-24"><a href="#cb52-24" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-25"><a href="#cb52-25" aria-hidden="true" tabindex="-1"></a>    <span class="fu">pivot_longer</span>(<span class="at">cols =</span> <span class="sc">-</span>date, <span class="at">values_to =</span> <span class="st">&quot;Growth&quot;</span>, <span class="at">names_to =</span> <span class="st">&quot;Currency&quot;</span>) <span class="sc">|&gt;</span> </span>
<span id="cb52-26"><a href="#cb52-26" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-27"><a href="#cb52-27" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">Growth =</span> <span class="fu">coalesce</span>(Growth, <span class="dv">0</span>)) </span>
<span id="cb52-28"><a href="#cb52-28" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-29"><a href="#cb52-29" aria-hidden="true" tabindex="-1"></a><span class="co"># Change to xts format</span></span>
<span id="cb52-30"><a href="#cb52-30" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-31"><a href="#cb52-31" aria-hidden="true" tabindex="-1"></a>gwt_xts <span class="ot">&lt;-</span> gwt <span class="sc">|&gt;</span> </span>
<span id="cb52-32"><a href="#cb52-32" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb52-33"><a href="#cb52-33" aria-hidden="true" tabindex="-1"></a>    <span class="fu">tbl_xts</span>(<span class="at">cols_to_xts =</span> Growth, <span class="at">spread_by =</span> Currency)</span>
<span id="cb52-34"><a href="#cb52-34" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-35"><a href="#cb52-35" aria-hidden="true" tabindex="-1"></a><span class="co"># MV Portmanteau tests</span></span>
<span id="cb52-36"><a href="#cb52-36" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb52-37"><a href="#cb52-37" aria-hidden="true" tabindex="-1"></a><span class="fu">MarchTest</span>(gwt_xts)</span></code></pre></div>
<pre><code>## Q(m) of squared series(LM test):  
## Test statistic:  3325.208  p-value:  0 
## Rank-based Test:  
## Test statistic:  2572.241  p-value:  0 
## Q_k(m) of squared series:  
## Test statistic:  10552.27  p-value:  0 
## Robust Test(5%) :  4017.407  p-value:  0</code></pre>
<p>The MARCH test indicates that all the MV portmanteau tests reject the
null of no conditional heteroskedasticity, motivating our use of MVGARCH
models.</p>
<h2 id="dcc-mv-garch-model">DCC MV-GARCH MODEL</h2>
<p>I decide to use a DCC MVGARCH Model; DCC models offer a simple and
more parsimonious means of doing MV-vol modelling. In particular, it
relaxes the constraint of a fixed correlation structure (assumed by the
CCC model), to allow for estimates of time-varying correlation.</p>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb54-1"><a href="#cb54-1" aria-hidden="true" tabindex="-1"></a><span class="co"># As in the tut, I select a VAR order of zero for the mean equation, and simply use the mean of each series.</span></span>
<span id="cb54-2"><a href="#cb54-2" aria-hidden="true" tabindex="-1"></a><span class="co"># The mean equation is thus in our case simply: Growth = mean(Growth) + et</span></span>
<span id="cb54-3"><a href="#cb54-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb54-4"><a href="#cb54-4" aria-hidden="true" tabindex="-1"></a><span class="co"># Then, for every series, a standard univariate GARCH(1,1) is run - giving us:</span></span>
<span id="cb54-5"><a href="#cb54-5" aria-hidden="true" tabindex="-1"></a><span class="co"># et and sigmat, which is then used to calculate the standardized resids, zt, which is used in DCC calcs after.</span></span>
<span id="cb54-6"><a href="#cb54-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb54-7"><a href="#cb54-7" aria-hidden="true" tabindex="-1"></a>DCCPre <span class="ot">&lt;-</span> <span class="fu">dccPre</span>(gwt_xts, <span class="at">include.mean =</span> F, <span class="at">p=</span><span class="dv">0</span>) <span class="co"># Find a nice way to put this in a table</span></span></code></pre></div>
<pre><code>## Component:  1 
## Estimates:  0.000002 0.11241 0.876699 
## se.coef  :  0 0.01066 0.011197 
## t-value  :  4.713568 10.54539 78.30072 
## Component:  2 
## Estimates:  0 0.076693 0.909835 
## se.coef  :  0 0.008226 0.00917 
## t-value  :  6.018786 9.323117 99.21955 
## Component:  3 
## Estimates:  0 0.056661 0.93898 
## se.coef  :  0 0.006052 0.00633 
## t-value  :  3.44769 9.362551 148.3287 
## Component:  4 
## Estimates:  0.000002 0.064515 0.918481 
## se.coef  :  0 0.007442 0.009899 
## t-value  :  4.12428 8.669312 92.78232 
## Component:  5 
## Estimates:  0.000002 0.145313 0.840736 
## se.coef  :  0 0.013067 0.014012 
## t-value  :  4.985724 11.12096 60.00209 
## Component:  6 
## Estimates:  0 0.040636 0.955979 
## se.coef  :  0 0.00391 0.003948 
## t-value  :  3.222423 10.39394 242.1655</code></pre>
<div class="sourceCode" id="cb56"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb56-1"><a href="#cb56-1" aria-hidden="true" tabindex="-1"></a>Vol <span class="ot">&lt;-</span> DCCPre<span class="sc">$</span>marVol</span>
<span id="cb56-2"><a href="#cb56-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-3"><a href="#cb56-3" aria-hidden="true" tabindex="-1"></a><span class="fu">colnames</span>(Vol) <span class="ot">&lt;-</span> <span class="fu">colnames</span>(gwt_xts)</span>
<span id="cb56-4"><a href="#cb56-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-5"><a href="#cb56-5" aria-hidden="true" tabindex="-1"></a>Vol <span class="ot">&lt;-</span> </span>
<span id="cb56-6"><a href="#cb56-6" aria-hidden="true" tabindex="-1"></a>  <span class="fu">data.frame</span>( <span class="fu">cbind</span>( <span class="at">date =</span> <span class="fu">index</span>(gwt_xts), Vol)) <span class="sc">|&gt;</span>  <span class="co"># Add date column which dropped away...</span></span>
<span id="cb56-7"><a href="#cb56-7" aria-hidden="true" tabindex="-1"></a>  <span class="fu">mutate</span>(<span class="at">date =</span> <span class="fu">as.Date</span>(date)) <span class="sc">|&gt;</span>  tibble<span class="sc">::</span><span class="fu">as_tibble</span>()  <span class="co"># make date column a date column...</span></span>
<span id="cb56-8"><a href="#cb56-8" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-9"><a href="#cb56-9" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-10"><a href="#cb56-10" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-11"><a href="#cb56-11" aria-hidden="true" tabindex="-1"></a>TidyVol <span class="ot">&lt;-</span> Vol <span class="sc">|&gt;</span>  <span class="fu">pivot_longer</span>(<span class="at">names_to =</span> <span class="st">&quot;Stocks&quot;</span>, <span class="at">values_to =</span>  <span class="st">&quot;Sigma&quot;</span>, <span class="at">cols =</span>  <span class="sc">-</span>date)</span>
<span id="cb56-12"><a href="#cb56-12" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-13"><a href="#cb56-13" aria-hidden="true" tabindex="-1"></a>TidyVol_plot <span class="ot">&lt;-</span> TidyVol <span class="sc">|&gt;</span> <span class="fu">ggplot</span>() <span class="sc">+</span> </span>
<span id="cb56-14"><a href="#cb56-14" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb56-15"><a href="#cb56-15" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(date, Sigma , <span class="at">color =</span> Stocks), <span class="at">size =</span> <span class="fl">0.9</span>, <span class="at">alpha =</span> <span class="fl">0.6</span>) <span class="sc">+</span></span>
<span id="cb56-16"><a href="#cb56-16" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb56-17"><a href="#cb56-17" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb56-18"><a href="#cb56-18" aria-hidden="true" tabindex="-1"></a>   fmxdat<span class="sc">::</span><span class="fu">theme_fmx</span>(<span class="at">title.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">30</span>), </span>
<span id="cb56-19"><a href="#cb56-19" aria-hidden="true" tabindex="-1"></a>                    <span class="at">subtitle.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">0</span>),</span>
<span id="cb56-20"><a href="#cb56-20" aria-hidden="true" tabindex="-1"></a>                    <span class="at">caption.size =</span> fmxdat<span class="sc">::</span><span class="fu">ggpts</span>(<span class="dv">25</span>),</span>
<span id="cb56-21"><a href="#cb56-21" aria-hidden="true" tabindex="-1"></a>                    <span class="at">CustomCaption =</span> T) <span class="sc">+</span> </span>
<span id="cb56-22"><a href="#cb56-22" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb56-23"><a href="#cb56-23" aria-hidden="true" tabindex="-1"></a>  fmxdat<span class="sc">::</span><span class="fu">fmx_cols</span>() <span class="sc">+</span> </span>
<span id="cb56-24"><a href="#cb56-24" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb56-25"><a href="#cb56-25" aria-hidden="true" tabindex="-1"></a>  <span class="fu">labs</span>(<span class="at">x =</span> <span class="st">&quot;&quot;</span>, <span class="at">y =</span> <span class="st">&quot;Sigma&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Note:</span><span class="sc">\n</span><span class="st">Calculation own&quot;</span>,</span>
<span id="cb56-26"><a href="#cb56-26" aria-hidden="true" tabindex="-1"></a>       <span class="at">title =</span> <span class="st">&quot;DCC GARCH: Estimated Volatility (Sigma) for Each Currency&quot;</span>,</span>
<span id="cb56-27"><a href="#cb56-27" aria-hidden="true" tabindex="-1"></a>       <span class="at">subtitle =</span> <span class="st">&quot;Notice this includes the Bloomberg Dollar Spot Index (BBDXY)&quot;</span>)</span>
<span id="cb56-28"><a href="#cb56-28" aria-hidden="true" tabindex="-1"></a>    </span>
<span id="cb56-29"><a href="#cb56-29" aria-hidden="true" tabindex="-1"></a><span class="co"># And finally touches with finplot    </span></span>
<span id="cb56-30"><a href="#cb56-30" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb56-31"><a href="#cb56-31" aria-hidden="true" tabindex="-1"></a>fmxdat<span class="sc">::</span><span class="fu">finplot</span>(TidyVol_plot, <span class="at">x.vert =</span> T, <span class="at">x.date.type =</span> <span class="st">&quot;%Y&quot;</span>, <span class="at">x.date.dist =</span> <span class="st">&quot;2 years&quot;</span>, <span class="at">darkcol =</span> F)</span></code></pre></div>
<p><img
src="README_files/figure-html/unnamed-chunk-6-1.png" /><!-- --></p>
<p>The volatility estimates are slightly different than the simple
sample SD graph above, in that the ZAR’s volatility has increased
relative to the other currencies, however, Brazil and Turkey still
showcases more volatility, even in the recent few years.</p>
<h2 id="calculating-the-dcc-model">Calculating the DCC Model</h2>
<p>I will now use the standardized residuals to calculate the DCC
Model</p>
<div class="sourceCode" id="cb57"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb57-1"><a href="#cb57-1" aria-hidden="true" tabindex="-1"></a><span class="co"># The standardized residuals</span></span>
<span id="cb57-2"><a href="#cb57-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb57-3"><a href="#cb57-3" aria-hidden="true" tabindex="-1"></a>StdRes <span class="ot">&lt;-</span> DCCPre<span class="sc">$</span>sresi</span>
<span id="cb57-4"><a href="#cb57-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb57-5"><a href="#cb57-5" aria-hidden="true" tabindex="-1"></a><span class="co"># I first do the detach trick from the tut:</span></span>
<span id="cb57-6"><a href="#cb57-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb57-7"><a href="#cb57-7" aria-hidden="true" tabindex="-1"></a>pacman<span class="sc">::</span><span class="fu">p_load</span>(tidyverse,fmxdat, rmsfuns, tbl2xts, tidyr, ggpubr, broom,rstatix, modelr )</span>
<span id="cb57-8"><a href="#cb57-8" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb57-9"><a href="#cb57-9" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:tidyverse&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-10"><a href="#cb57-10" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:fmxdat&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-11"><a href="#cb57-11" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:rmsfuns&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-12"><a href="#cb57-12" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:tbl2xts&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-13"><a href="#cb57-13" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:ggpubr&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-14"><a href="#cb57-14" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:rstatix&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-15"><a href="#cb57-15" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:modelr&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-16"><a href="#cb57-16" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:broom&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-17"><a href="#cb57-17" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:tidyr&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span>
<span id="cb57-18"><a href="#cb57-18" aria-hidden="true" tabindex="-1"></a><span class="fu">detach</span>(<span class="st">&quot;package:dplyr&quot;</span>, <span class="at">unload=</span><span class="cn">TRUE</span>)</span></code></pre></div>
<pre><code>## Warning: &#39;dplyr&#39; namespace cannot be unloaded:
##   namespace &#39;dplyr&#39; is imported by &#39;dbplyr&#39; so cannot be unloaded</code></pre>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb59-1"><a href="#cb59-1" aria-hidden="true" tabindex="-1"></a>DCC <span class="ot">&lt;-</span> <span class="fu">dccFit</span>(StdRes,<span class="at">type =</span> <span class="st">&quot;Engle&quot;</span>) </span></code></pre></div>
<pre><code>## Warning in sqrt(diag(Hi)): NaNs produced</code></pre>
<pre><code>## Estimates:  0.95 0.02370479 8.561218 
## st.errors:  NaN NaN 0.3259811 
## t-values:   NaN NaN 26.26292</code></pre>
<div class="sourceCode" id="cb62"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb62-1"><a href="#cb62-1" aria-hidden="true" tabindex="-1"></a>pacman<span class="sc">::</span><span class="fu">p_load</span>(tidyverse,fmxdat, rmsfuns, tbl2xts, tidyr, ggpubr, broom,rstatix, modelr )</span></code></pre></div>
<div class="sourceCode" id="cb63"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb63-1"><a href="#cb63-1" aria-hidden="true" tabindex="-1"></a>Rhot <span class="ot">&lt;-</span> DCC<span class="sc">$</span>rho.t</span>
<span id="cb63-2"><a href="#cb63-2" aria-hidden="true" tabindex="-1"></a><span class="co"># Right, so it gives us all the columns together in the form:</span></span>
<span id="cb63-3"><a href="#cb63-3" aria-hidden="true" tabindex="-1"></a><span class="co"># X1,X1 ; X1,X2 ; X1,X3 ; ....</span></span>
<span id="cb63-4"><a href="#cb63-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-5"><a href="#cb63-5" aria-hidden="true" tabindex="-1"></a><span class="co"># So, let&#39;s be clever about defining more informative col names. </span></span>
<span id="cb63-6"><a href="#cb63-6" aria-hidden="true" tabindex="-1"></a><span class="co"># I will create a renaming function below:</span></span>
<span id="cb63-7"><a href="#cb63-7" aria-hidden="true" tabindex="-1"></a>ReturnSeries <span class="ot">=</span> gwt_xts</span>
<span id="cb63-8"><a href="#cb63-8" aria-hidden="true" tabindex="-1"></a>DCC.TV.Cor <span class="ot">=</span> Rhot</span>
<span id="cb63-9"><a href="#cb63-9" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-10"><a href="#cb63-10" aria-hidden="true" tabindex="-1"></a>renamingdcc <span class="ot">&lt;-</span> <span class="cf">function</span>(ReturnSeries, DCC.TV.Cor) {</span>
<span id="cb63-11"><a href="#cb63-11" aria-hidden="true" tabindex="-1"></a>  </span>
<span id="cb63-12"><a href="#cb63-12" aria-hidden="true" tabindex="-1"></a>ncolrtn <span class="ot">&lt;-</span> <span class="fu">ncol</span>(ReturnSeries)</span>
<span id="cb63-13"><a href="#cb63-13" aria-hidden="true" tabindex="-1"></a>namesrtn <span class="ot">&lt;-</span> <span class="fu">colnames</span>(ReturnSeries)</span>
<span id="cb63-14"><a href="#cb63-14" aria-hidden="true" tabindex="-1"></a><span class="fu">paste</span>(namesrtn, <span class="at">collapse =</span> <span class="st">&quot;_&quot;</span>)</span>
<span id="cb63-15"><a href="#cb63-15" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-16"><a href="#cb63-16" aria-hidden="true" tabindex="-1"></a>nam <span class="ot">&lt;-</span> <span class="fu">c</span>()</span>
<span id="cb63-17"><a href="#cb63-17" aria-hidden="true" tabindex="-1"></a>xx <span class="ot">&lt;-</span> <span class="fu">mapply</span>(rep, <span class="at">times =</span> ncolrtn<span class="sc">:</span><span class="dv">1</span>, <span class="at">x =</span> namesrtn)</span>
<span id="cb63-18"><a href="#cb63-18" aria-hidden="true" tabindex="-1"></a><span class="co"># Now let&#39;s be creative in designing a nested for loop to save the names corresponding to the columns of interest.. </span></span>
<span id="cb63-19"><a href="#cb63-19" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-20"><a href="#cb63-20" aria-hidden="true" tabindex="-1"></a><span class="co"># TIP: draw what you want to achieve on a paper first. Then apply code.</span></span>
<span id="cb63-21"><a href="#cb63-21" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-22"><a href="#cb63-22" aria-hidden="true" tabindex="-1"></a><span class="co"># See if you can do this on your own first.. Then check vs my solution:</span></span>
<span id="cb63-23"><a href="#cb63-23" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-24"><a href="#cb63-24" aria-hidden="true" tabindex="-1"></a>nam <span class="ot">&lt;-</span> <span class="fu">c</span>()</span>
<span id="cb63-25"><a href="#cb63-25" aria-hidden="true" tabindex="-1"></a><span class="cf">for</span> (j <span class="cf">in</span> <span class="dv">1</span><span class="sc">:</span>(ncolrtn)) {</span>
<span id="cb63-26"><a href="#cb63-26" aria-hidden="true" tabindex="-1"></a><span class="cf">for</span> (i <span class="cf">in</span> <span class="dv">1</span><span class="sc">:</span>(ncolrtn)) {</span>
<span id="cb63-27"><a href="#cb63-27" aria-hidden="true" tabindex="-1"></a>  nam[(i <span class="sc">+</span> (j<span class="dv">-1</span>)<span class="sc">*</span>(ncolrtn))] <span class="ot">&lt;-</span> <span class="fu">paste</span>(xx[[j]][<span class="dv">1</span>], xx[[i]][<span class="dv">1</span>], <span class="at">sep=</span><span class="st">&quot;_&quot;</span>)</span>
<span id="cb63-28"><a href="#cb63-28" aria-hidden="true" tabindex="-1"></a>}</span>
<span id="cb63-29"><a href="#cb63-29" aria-hidden="true" tabindex="-1"></a>}</span>
<span id="cb63-30"><a href="#cb63-30" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-31"><a href="#cb63-31" aria-hidden="true" tabindex="-1"></a><span class="fu">colnames</span>(DCC.TV.Cor) <span class="ot">&lt;-</span> nam</span>
<span id="cb63-32"><a href="#cb63-32" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-33"><a href="#cb63-33" aria-hidden="true" tabindex="-1"></a><span class="co"># So to plot all the time-varying correlations wrt SBK:</span></span>
<span id="cb63-34"><a href="#cb63-34" aria-hidden="true" tabindex="-1"></a> <span class="co"># First append the date column that has (again) been removed...</span></span>
<span id="cb63-35"><a href="#cb63-35" aria-hidden="true" tabindex="-1"></a>DCC.TV.Cor <span class="ot">&lt;-</span> </span>
<span id="cb63-36"><a href="#cb63-36" aria-hidden="true" tabindex="-1"></a>    <span class="fu">data.frame</span>( <span class="fu">cbind</span>( <span class="at">date =</span> <span class="fu">index</span>(ReturnSeries), DCC.TV.Cor)) <span class="sc">%&gt;%</span> <span class="co"># Add date column which dropped away...</span></span>
<span id="cb63-37"><a href="#cb63-37" aria-hidden="true" tabindex="-1"></a>    <span class="fu">mutate</span>(<span class="at">date =</span> <span class="fu">as.Date</span>(date)) <span class="sc">%&gt;%</span>  <span class="fu">tbl_df</span>() </span>
<span id="cb63-38"><a href="#cb63-38" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-39"><a href="#cb63-39" aria-hidden="true" tabindex="-1"></a>DCC.TV.Cor <span class="ot">&lt;-</span> DCC.TV.Cor <span class="sc">%&gt;%</span> <span class="fu">gather</span>(Pairs, Rho, <span class="sc">-</span>date)</span>
<span id="cb63-40"><a href="#cb63-40" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-41"><a href="#cb63-41" aria-hidden="true" tabindex="-1"></a>DCC.TV.Cor</span>
<span id="cb63-42"><a href="#cb63-42" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-43"><a href="#cb63-43" aria-hidden="true" tabindex="-1"></a>}</span>
<span id="cb63-44"><a href="#cb63-44" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb63-45"><a href="#cb63-45" aria-hidden="true" tabindex="-1"></a><span class="co"># Let&#39;s see if our function works! Excitement!</span></span>
<span id="cb63-46"><a href="#cb63-46" aria-hidden="true" tabindex="-1"></a>Rhot <span class="ot">&lt;-</span> </span>
<span id="cb63-47"><a href="#cb63-47" aria-hidden="true" tabindex="-1"></a>  <span class="fu">renamingdcc</span>(<span class="at">ReturnSeries =</span> gwt_xts, <span class="at">DCC.TV.Cor =</span> Rhot)</span></code></pre></div>
<pre><code>## Warning: `tbl_df()` was deprecated in dplyr 1.0.0.
## ℹ Please use `tibble::as_tibble()` instead.</code></pre>
<div class="sourceCode" id="cb65"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb65-1"><a href="#cb65-1" aria-hidden="true" tabindex="-1"></a><span class="fu">head</span>(Rhot <span class="sc">%&gt;%</span> <span class="fu">arrange</span>(date))</span></code></pre></div>
<pre><code>## # A tibble: 6 × 3
##   date       Pairs                Rho
##   &lt;date&gt;     &lt;chr&gt;              &lt;dbl&gt;
## 1 2005-01-03 Brazil_Brazil      1    
## 2 2005-01-03 Brazil_India       0.159
## 3 2005-01-03 Brazil_Poland      0.377
## 4 2005-01-03 Brazil_SouthAfrica 0.504
## 5 2005-01-03 Brazil_Turkey      0.411
## 6 2005-01-03 Brazil_BBDXY       0.380</code></pre>
<p>And now I create a plot the ZAR relative to the other currencies</p>
<div class="sourceCode" id="cb67"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb67-1"><a href="#cb67-1" aria-hidden="true" tabindex="-1"></a><span class="co"># Let&#39;s now create a plot for all the stocks relative to the other stocks...</span></span>
<span id="cb67-2"><a href="#cb67-2" aria-hidden="true" tabindex="-1"></a>g1 <span class="ot">&lt;-</span> </span>
<span id="cb67-3"><a href="#cb67-3" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggplot</span>(Rhot <span class="sc">|&gt;</span>  <span class="fu">filter</span>(<span class="fu">grepl</span>(<span class="st">&quot;SouthAfrica_&quot;</span>, Pairs ), <span class="sc">!</span><span class="fu">grepl</span>(<span class="st">&quot;_SouthAfrica&quot;</span>, Pairs)) ) <span class="sc">+</span> </span>
<span id="cb67-4"><a href="#cb67-4" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(<span class="at">x =</span> date, <span class="at">y =</span> Rho, <span class="at">colour =</span> Pairs)) <span class="sc">+</span> </span>
<span id="cb67-5"><a href="#cb67-5" aria-hidden="true" tabindex="-1"></a>  <span class="fu">theme_hc</span>() <span class="sc">+</span></span>
<span id="cb67-6"><a href="#cb67-6" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggtitle</span>(<span class="st">&quot;Dynamic Conditional Correlations: South Africa (ZAR)&quot;</span>)</span>
<span id="cb67-7"><a href="#cb67-7" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb67-8"><a href="#cb67-8" aria-hidden="true" tabindex="-1"></a><span class="fu">print</span>(g1)</span></code></pre></div>
<p><img
src="README_files/figure-html/unnamed-chunk-9-1.png" /><!-- --></p>
<p>For a clearer picture, lets only plot the SouthAfrica_BBDXY plot</p>
<div class="sourceCode" id="cb68"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb68-1"><a href="#cb68-1" aria-hidden="true" tabindex="-1"></a>g2 <span class="ot">&lt;-</span> </span>
<span id="cb68-2"><a href="#cb68-2" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggplot</span>(Rhot <span class="sc">|&gt;</span>  <span class="fu">filter</span>(Pairs <span class="sc">%in%</span> <span class="fu">c</span>(<span class="st">&quot;SouthAfrica_BBDXY&quot;</span>))) <span class="sc">+</span> </span>
<span id="cb68-3"><a href="#cb68-3" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_line</span>(<span class="fu">aes</span>(<span class="at">x =</span> date, <span class="at">y =</span> Rho, <span class="at">colour =</span> Pairs)) <span class="sc">+</span> </span>
<span id="cb68-4"><a href="#cb68-4" aria-hidden="true" tabindex="-1"></a>  <span class="fu">theme_hc</span>() <span class="sc">+</span></span>
<span id="cb68-5"><a href="#cb68-5" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggtitle</span>(<span class="st">&quot;Dynamic Conditional Correlation: South Africa (ZAR) and BBDXY&quot;</span>)</span>
<span id="cb68-6"><a href="#cb68-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb68-7"><a href="#cb68-7" aria-hidden="true" tabindex="-1"></a><span class="fu">print</span>(g2)</span></code></pre></div>
<p><img
src="README_files/figure-html/unnamed-chunk-10-1.png" /><!-- --></p>
