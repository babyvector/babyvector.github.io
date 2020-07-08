---
layout: post
title: lwip使用
---

在main()函数中调用下面这个函数进行从单片机到PC发送数据的初始化

    tcp_send_init(){
        
        struct tcp_pcb *pcb;
        struct ip_addr ipaddr;
        err_t err;
        u16_t port;

        pcb = tcp_new();

        IP4_ADDR(&ipaddr,  192, 168,   1, 209);
        port = 7;

        err = tcp_connect(pcb, &ipaddr, port, tcp_connected_callback);

    }

当一次tcp传送完成之后，调用下面的函数(注册的回调函数)

    /*this fuction just used to count the tcp transmission times*/
    static err_t
    tcp_sent_callback(void *arg, struct tcp_pcb *tpcb, u16_t len)
    {

        tcp_trans_done ++;
        return ERR_OK;
    }

当连接建立连接之后调用的函数(注册的回调函数)


        static err_t
        tcp_connected_callback(void *arg, struct tcp_pcb *tpcb, err_t err)
        {

            /* store state */
            connected_pcb = tpcb;

            /* set callback values & functions */
            tcp_arg(tpcb, NULL);
            tcp_sent(tpcb, tcp_sent_callback);

            tcp_client_connected = 1;

            /* initiate data transfer */
            return ERR_OK;
        }

在main()函数中真正进行发送的函数

* tcp_sndbuf();
* tcp_write();
* tcp_output();

        void send_received_data()
        {

            if (!connected_pcb)
                return;

            /* if tcp send buffer has enough space to hold the data we want to transmit from PL, then start tcp transmission*/
            if (tcp_sndbuf(tpcb) > SEND_SIZE)
            {
                /*transmit received data through TCP*/
                err = tcp_write(tpcb, RX_BUFFER_BASE, SEND_SIZE, copy);

                err = tcp_output(tpcb);
            
        }
        