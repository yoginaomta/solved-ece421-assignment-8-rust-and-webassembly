Download Link: https://assignmentchef.com/product/solved-ece421-assignment-8-rust-and-webassembly
<br>
<strong>Question 1:</strong> WebAssembly is a small, well-defined language. It is specified using mainly sequent calculus. You can find the specification here:  <a href="https://webassembly.github.io/spec/core/valid/instructions.html">https://webassembly.github.io/spec/core/valid/instructions.html</a>

Given this specification and notational conventions, we define several lower-level functions in

WebAssembly. Write sequent calculus definitions for the following functions; a- “The instruction t.const results in the same generic type.”

b- “The instruction t.add takes two generic values and returns the same generic type” c- “The instruction t.eq takes two generic values and returns in an i32 value.” Yes, WebAssembly implements Boolean as an Integer.




<strong>Question 2:</strong> Consider the following piece of code:

<table width="639">

 <tbody>

  <tr>

   <td width="639">1     use hyper::rt::Future;2     use hyper::service::service_fn_ok;3     use hyper::{Body, Request, Response, Server};78        fn main() {9        let addr = (<strong>[127, 0, 0, 1]</strong>, <strong>3000</strong>).into();10       let server = Server::bind(&amp;addr)11       .serve(|| {             12         service_fn(service_router)13          })14          .map_err(|e| eprintln!(“server error: {}”, e));1516     println!(“Listening on http://{}”, addr);17     hyper::rt::run(server);18     }1920                fn svc_wait(t: u64) -&gt; impl <strong>Future</strong>&lt;Item = (), Error = ()&gt; {21                println!(“[start] waiting…”);22                let when = Instant::now() + Duration::from_millis(t);23                Delay::new(when)24                .map_err(|e| panic!(“timer failed; err={:?}”, e))25                .and_then(|_| {26                println!(“[end] waiting”);27                Ok(())28                })29                }303132  fn fetch_data() -&gt; impl <strong>Future</strong>&lt;Item = future::FutureResult&lt;RespStruct, 33         String&gt;, Error = ()&gt; {34       let uri: Uri = “<strong>http://httpbin.org/get</strong>“.parse().expect(“Cannot parse35       URL”);36       Client::new()37       .get(uri)38       // Future is polled here39       .and_then(|res| { 40              res.into_body().concat2()41                })42                .map_err(|err| println!(“error: {}”, err))</td>

  </tr>

  <tr>

   <td width="639">43                       .map(|body| {44                       let decoded: RespStruct =45                       serde_json::from_slice(<strong>&amp;body</strong>).expect(“Couldn’t deserialize”);46                       future::ok(decoded)47                       })48                       }495051                                                                                               type <strong>BoxFut</strong> = Box&lt;dyn Future&lt;Item = Response&lt;Body&gt;, Error = hyper::Error&gt;52                                                                                               + Send&gt;;535455          fn service_router(req: Request&lt;Body&gt;) -&gt; BoxFut {56          let mut response = Response::new(Body::empty());5758      match (req.method(), req.uri().path()) {606162                               (&amp;Method::GET, “/wait”) =&gt; {63                               let r = svc_wait(1500);64                               hyper::rt::spawn(r);65                               *response.body_mut() = Body::from(format!(“Triggered waiting66                               {}ms”, 1500));67                               }686970                             (&amp;Method::GET, “/fetch”) =&gt; {71                             let r = fetch_data().map(|x| {72                             println!(“got data: {:?}”, x);73                             });74                             hyper::rt::spawn(r);75                             *response.body_mut() = Body::from(“Sent request to external76                             webservice”);77                             }7879       // … more routers80       }81       eprintln!(“Returning a response”);82       Box::new(future::ok(response)) 83  }</td>

  </tr>

 </tbody>

</table>

<ul>

 <li>Explain what do the numbers mean in line 9.</li>

 <li>The function in line 20 uses <strong>Future</strong>; what is Future?</li>

 <li>What does <a href="https://httpbin.org/get">http://httpbin.org</a> do (line 34)?</li>

 <li>Give a definition for the <strong>body</strong> variable in line 45.</li>

 <li>Explain the <strong>BoxFut</strong> type in line 51 f- Should <strong>BoxFut</strong> (Line 51) implement the Sync trait? g- Should <strong>BoxFut </strong>(Line 51) use a lifetime?</li>

</ul>

h- At some points, you will be using the following instruction:

<em>$ curl localhost:3000/wait </em>

What does <strong>curl</strong> do?

Does this code use Async/IO, if not, how would you change the program to use it? <strong>Question 3:</strong>




<strong>Question 3: </strong>Libra (<a href="http://libra.org/">libra.org</a><a href="http://libra.org/">)</a> is a major new product from Facebook. Libra is a cryptocurrency platform. Facebook expect to make billions from Libra and revolutionize the financial industry.

a- What language is Libra written in? b- Discuss the technical reasons why this choice of language suits the application and its objectives.

c- Libra uses many standard packages, including lazy_static, tokio, failure, etc. Briefly, describe each of these packages.

<strong>Question 4: </strong>Consider the following program:

a- What is nighty channel in Rust (check Playground) b- What are unstable features? c- Why can playground run this code (think O.S.) d- What is the output from this code?

e- Provide comments for the lines ending in #




<table width="639">

 <tbody>

  <tr>

   <td width="639">#![feature(asm)] fn main() {let message = String::from(“James, you are completely mad
”);     syscall(message);} #[cfg(target_os = “linux”)] fn syscall(message: String) {     let msg_ptr = message.as_ptr();     let len = message.len();     unsafe {         asm!(”mov     $$1, %rax   #         mov     $$1, %rdi   #          mov     $0, %rsi    #          mov     $1, %rdx    #          syscall             #”         :: “r”(msg_ptr), “r”(len))}}</td>

  </tr>

 </tbody>

</table>


