
Issue One

<pre data-line="" class=" line-numbers  language-js"><code class="  language-js">using <span class="token punctuation">{</span> sap<span class="token punctuation">.</span>capire<span class="token punctuation">.</span>incidents <span class="token keyword">as</span> my <span class="token punctuation">}</span> <span class="token keyword">from</span> <span class="token string">'../db/schema'</span><span class="token punctuation">;</span>

service ProcessorService <span class="token punctuation">{</span> 
    entity Incidents <span class="token keyword">as</span> projection on my<span class="token punctuation">.</span>Incidents<span class="token punctuation">;</span>

    @readonly
    entity Customers <span class="token keyword">as</span> projection on my<span class="token punctuation">.</span>Customers<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
<span aria-hidden="true" class="line-numbers-rows"><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span></span></code></pre>

Issue Two

<pre data-line="" class=" line-numbers  language-js"><code class="  language-js"><span class="token keyword">const</span> cds <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'@sap/cds'</span><span class="token punctuation">)</span>

<span class="token keyword">class</span> <span class="token class-name">ProcessorService</span> <span class="token keyword">extends</span> <span class="token class-name">cds<span class="token punctuation">.</span>ApplicationService</span> <span class="token punctuation">{</span>
  <span class="token comment">/** Registering custom event handlers */</span>
  <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">before</span><span class="token punctuation">(</span><span class="token string">"UPDATE"</span><span class="token punctuation">,</span> <span class="token string">"Incidents"</span><span class="token punctuation">,</span> <span class="token punctuation">(</span>req<span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">onUpdate</span><span class="token punctuation">(</span>req<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">before</span><span class="token punctuation">(</span><span class="token string">"CREATE"</span><span class="token punctuation">,</span> <span class="token string">"Incidents"</span><span class="token punctuation">,</span> <span class="token punctuation">(</span>req<span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">changeUrgencyDueToSubject</span><span class="token punctuation">(</span>req<span class="token punctuation">.</span>data<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">return</span> <span class="token keyword">super</span><span class="token punctuation">.</span><span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token function">changeUrgencyDueToSubject</span><span class="token punctuation">(</span>data<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>data<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">const</span> incidents <span class="token operator">=</span> Array<span class="token punctuation">.</span><span class="token function">isArray</span><span class="token punctuation">(</span>data<span class="token punctuation">)</span> <span class="token operator">?</span> data <span class="token punctuation">:</span> <span class="token punctuation">[</span>data<span class="token punctuation">]</span><span class="token punctuation">;</span>
      incidents<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span><span class="token punctuation">(</span>incident<span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>incident<span class="token punctuation">.</span>title<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">toLowerCase</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">includes</span><span class="token punctuation">(</span><span class="token string">"urgent"</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
          incident<span class="token punctuation">.</span>urgency <span class="token operator">=</span> <span class="token punctuation">{</span> code<span class="token punctuation">:</span> <span class="token string">"H"</span><span class="token punctuation">,</span> descr<span class="token punctuation">:</span> <span class="token string">"High"</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>

  <span class="token comment">/** Custom Validation */</span>
  <span class="token keyword">async</span> <span class="token function">onUpdate</span> <span class="token punctuation">(</span>req<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">const</span> <span class="token punctuation">{</span> status_code <span class="token punctuation">}</span> <span class="token operator">=</span> <span class="token keyword">await</span> <span class="token constant">SELECT</span><span class="token punctuation">.</span><span class="token function">one</span><span class="token punctuation">(</span>req<span class="token punctuation">.</span>subject<span class="token punctuation">,</span> i <span class="token operator">=&gt;</span> i<span class="token punctuation">.</span>status_code<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">where</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token constant">ID</span><span class="token punctuation">:</span> req<span class="token punctuation">.</span>data<span class="token punctuation">.</span><span class="token constant">ID</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>status_code <span class="token operator">===</span> <span class="token string">'C'</span><span class="token punctuation">)</span>
      <span class="token keyword">return</span> req<span class="token punctuation">.</span><span class="token function">reject</span><span class="token punctuation">(</span><span class="token template-string"><span class="token string">`Can't modify a closed incident`</span></span><span class="token punctuation">)</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
module<span class="token punctuation">.</span>exports <span class="token operator">=</span> ProcessorService
<span aria-hidden="true" class="line-numbers-rows"><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span><span></span></span></code></pre>


## Github Rendering Issues

http://\<IP of your SAP CAL backend\>:8443/.

http://\<IP of your SAP CAL backend>:8443/.

\>

\<Messi>

Image_1_Surya

http://\<virtual host name>:\<virtual port name>/\<OData service name>

`http://<virtual host name>:<virtual port name>/<OData service name>`

<img src="./Practice/Images/download.jpg" width="80%">

