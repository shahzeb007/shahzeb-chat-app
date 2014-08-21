shahzeb-chat-app
================

It is a simple chat application.
================================

server code 
===========
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using System.Runtime.Remoting;
using System.Runtime.Remoting.Channels;
using System.Runtime.Remoting.Channels.Tcp;
namespace ChatServer
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            TcpChannel tc = new TcpChannel(8051);
            ChannelServices.RegisterChannel(tc, false);
            RemotingConfiguration.RegisterWellKnownServiceType(typeof(ChatBL.ChatClass), "MeraObject", WellKnownObjectMode.Singleton);
            label1.Visible = true;
            label2.Visible = true;
            timer1.Enabled = true;
        }
        int count;
        private void timer1_Tick(object sender, EventArgs e)
        {
            if (count < 5)
            {
                label2.Text += " . ";
                count++;
                if (count == 5)
                {
                    label2.Text = "";
                    count = 0;
                }
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            this.Close();
        }
    }
}

PL code
=======
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace ChatPL
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            TempRef.PostChat(LoginName+" : "+ textBox1.Text);
            textBox1.Clear();
        }
        ChatBL.ChatClass TempRef;
        private void Form1_Load(object sender, EventArgs e)
        {
            TempRef = (ChatBL.ChatClass)Activator.GetObject(typeof(ChatBL.ChatClass), "tcp://127.0.0.1:8051/MeraObject");
            timer1.Enabled = true;
        }

        private void timer1_Tick(object sender, EventArgs e)
        {
           textBox2.Text= TempRef.GetChat();
        }
        string LoginName;
        private void button2_Click(object sender, EventArgs e)
        {
            if (textBox3.Text.Trim().Length > 0)
            {
                LoginName = textBox3.Text;
                button2.Visible = false;
                textBox1.Visible = true;
                textBox2.Visible = true;
                textBox3.Visible = false;
                button1.Visible = true;
                this.Text = "Welcome : " + LoginName;
            }
            else
            {
                ErrorProvider er = new ErrorProvider();
                er.SetError(textBox3, "Plz Enter Ur Name First!! ");
            }
        }
    }
}

BL code
=======
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ChatBL
{
    public class ChatClass:MarshalByRefObject
    {
        static string Msg;
        public string GetChat()
        {
            return Msg;
        }
        public void PostChat(string NewMsg)
        {
            Msg += NewMsg + Environment.NewLine;
        }
    }
}

