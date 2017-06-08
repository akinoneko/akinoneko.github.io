---
title: 解决request里getinputstream只能取一次值的问题
date: 2017-06-08 10:11:47
tags: Java
---

```
import java.io.IOException;



import javax.servlet.Filter;

import javax.servlet.FilterChain;

import javax.servlet.FilterConfig;

import javax.servlet.ServletException;

import javax.servlet.ServletRequest;

import javax.servlet.ServletResponse;

import javax.servlet.http.HttpServletRequest;





/**

 * 解决request里getinputstream只能获取一次值得问题

 *

 */



public class HttpServletRequestReplacedFilter implements Filter {  



	@Override

	public void destroy() {

		

	}



    @Override  

    public void doFilter(ServletRequest request, ServletResponse response,  

            FilterChain chain) throws IOException, ServletException {  

        ServletRequest requestWrapper = null;    

        if(request instanceof HttpServletRequest) { 

            requestWrapper = new MAPIHttpServletRequestWrapper((HttpServletRequest) request);    

        }    

        if(requestWrapper == null) {    

            chain.doFilter(request, response);    

        } else {    

            chain.doFilter(requestWrapper, response);    

        }    

    }  

  

	@Override

	public void init(FilterConfig filterConfig) throws ServletException {

		

	}

  

}  



--------------------------------------------------------------



import java.io.BufferedReader;

import java.io.ByteArrayInputStream;

import java.io.ByteArrayOutputStream;

import java.io.IOException;

import java.io.InputStream;

import java.io.InputStreamReader;



import javax.servlet.ServletInputStream;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletRequestWrapper;



/**

 * 此类用于request重新包装

 *

 */

public class MAPIHttpServletRequestWrapper extends HttpServletRequestWrapper {  

    

    private final byte[] body; // 报文  

    final static int BUFFER_SIZE = 4096;  

    public MAPIHttpServletRequestWrapper(HttpServletRequest request) throws IOException {  

        super(request);  

        body = InputStreamTOByte(request.getInputStream());  

    }  

      

    @Override  

    public BufferedReader getReader() throws IOException {  

        return new BufferedReader(new InputStreamReader(getInputStream()));  

    }  

      

    @Override  

    public ServletInputStream getInputStream() throws IOException {  

        final ByteArrayInputStream bais = new ByteArrayInputStream(body);  

        return new ServletInputStream() {  

              

            @Override  

            public int read() throws IOException {  

                return bais.read();  

            }  

        };  

    }  

    // 将InputStream转换成byte数组  

    public static byte[] InputStreamTOByte(InputStream in) throws IOException {  

  

        ByteArrayOutputStream outStream = new ByteArrayOutputStream();  

  

        byte[] data = new byte[BUFFER_SIZE];  

  

        int count = -1;  

  

        while ((count = in.read(data, 0, BUFFER_SIZE)) != -1)  

  

            outStream.write(data, 0, count);  

  

        data = null;  

  

        return outStream.toByteArray();  

  

    }  

}  
```