# c-2

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Data.SqlClient;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace BankamatikSimulasyonu
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {

        }
        SqlConnection baglanti = new SqlConnection("Data Source=NTB1000088;Initial Catalog=DbBankaTest;User ID=sa;Password=sa321sa?");
        private void lnkKayıtOl_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            Form3 frm = new Form3();
            
            frm.Show();

        }

        private void button1_Click(object sender, EventArgs e)
        {
            baglanti.Open();
            SqlCommand komut = new SqlCommand("Select * From TblKisiler where HesapNo=@p1 and Sifre=@p2", baglanti );
            komut.Parameters.AddWithValue("@p1", mskHesapNo.Text);
            komut.Parameters.AddWithValue("@p2", txtSifre.Text);
            SqlDataReader dr=komut.ExecuteReader();
            if(dr.Read())
            {
                Form2 fr = new Form2();
                fr.hesap = mskHesapNo.Text;
                fr.Show();
            }
            else
            {
                MessageBox.Show("Hatalı bilgi girdiniz!");

            }
            baglanti.Close();

        }
    }
}
Form2cs

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Data.SqlClient;


namespace BankamatikSimulasyonu
{
    public partial class Form2 : Form
    {
        public Form2()
        {
            InitializeComponent();
        }
        public string hesap;
        SqlConnection baglanti = new SqlConnection("Data Source=NTB1000088;Initial Catalog=DbBankaTest;User ID=sa;Password=sa321sa?");
        private void Form2_Load(object sender, EventArgs e)
        {
            baglanti.Open();
            lblHesapNo.Text = hesap;
            SqlCommand komut = new SqlCommand("Select * From TblKisiler where HesapNo=@p1", baglanti);
            komut.Parameters.AddWithValue("@p1", hesap);
            SqlDataReader dr= komut.ExecuteReader();    
            while (dr.Read())
            {
                lblAdSoyad.Text = dr[1] + " " + dr[2];
                lblTcKimlik.Text = dr[3].ToString();
                lblTelefon.Text = dr[4].ToString();
            }
            baglanti.Close();

        }

        private void btnGonder_Click(object sender, EventArgs e)
        {
            //gönderilen hesabın para artışı
            baglanti.Open();
            SqlCommand komut = new SqlCommand("update TblHesap set Bakiye=Bakiye+@p1 where HesapNo=@p2 ",baglanti);
            komut.Parameters.AddWithValue("@p1", decimal.Parse(txtTutar.Text));
            komut.Parameters.AddWithValue("@p2", mskHesapNo.Text);
            komut.ExecuteNonQuery();
            baglanti.Close();
            MessageBox.Show("İşlem gerçekleşti.");

            //gönderen hesabın para azalışı
            baglanti.Open();
            SqlCommand komut2 = new SqlCommand("update TblHesap set Bakiye=Bakiye-@k1 where HesapNo=@k2", baglanti);
            komut2.Parameters.AddWithValue("@k1", decimal.Parse(txtTutar.Text));
            komut2.Parameters.AddWithValue("@k2", hesap);
            komut2.ExecuteNonQuery();
            baglanti.Close();
            MessageBox.Show("İşlem gerçekleşti");

        }
    }
}
