Sample
======
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Data;
using System.IO;
using iTextSharp.text;
using iTextSharp.text.pdf;
using iTextSharp.text.html;
using iTextSharp.text.html.simpleparser;

namespace Thesis_2.classes
{
    public class ReportGens
    {
        public void doc(GridView gvdetails, string filename)
        {
            if(gvdetails.Rows[0].Cells[0].Text == "")
                gvdetails.Columns[0].Visible = false;
            HttpContext.Current.Response.Clear();
            HttpContext.Current.Response.Buffer = true;
            HttpContext.Current.Response.AddHeader("content-disposition", "attachment;filename=" + filename + ".doc");
            HttpContext.Current.Response.Charset = "";
            HttpContext.Current.Response.ContentType = "application/vnd.ms-word ";
            StringWriter sw = new StringWriter();
            HtmlTextWriter hw = new HtmlTextWriter(sw);
            gvdetails.AllowPaging = false;
            gvdetails.RenderControl(hw);
            HttpContext.Current.Response.Output.Write(sw.ToString());
            HttpContext.Current.Response.Flush();
            HttpContext.Current.Response.End();
        }

        public void xls(GridView gvdetails, string filename)
        {
            DataTable dtdata = new DataTable();
            if (gvdetails.HeaderRow != null)
            {

                for (int i = 0; i < gvdetails.HeaderRow.Cells.Count; i++)
                {
                    dtdata.Columns.Add(gvdetails.HeaderRow.Cells[i].Text);
                }
            }
            foreach (GridViewRow row in gvdetails.Rows)
            {
                DataRow dr;
                dr = dtdata.NewRow();
                for (int i = 0; i < row.Cells.Count; i++)
                {
                    dr[i] = row.Cells[i].Text.Replace(" ", "");
                }
                dtdata.Rows.Add(dr);
            }

            string attach = "attachment;filename=" + filename + ".xls";
            HttpContext.Current.Response.ClearContent();
            HttpContext.Current.Response.AddHeader("content-disposition", attach);
            HttpContext.Current.Response.ContentType = "application/ms-excel";
            if (dtdata != null)
            {
                foreach (DataColumn dc in dtdata.Columns)
                {
                    HttpContext.Current.Response.Write(dc.ColumnName + "\t");
                    //sep = ";";
                }
                HttpContext.Current.Response.Write(System.Environment.NewLine);
                foreach (DataRow dr in dtdata.Rows)
                {
                    for (int i = 0; i < dtdata.Columns.Count; i++)
                    {
                        HttpContext.Current.Response.Write(dr[i].ToString() + "\t");
                    }
                    HttpContext.Current.Response.Write("\n");
                }
                HttpContext.Current.Response.End();
            }
        }

        public void pdf(GridView gvdetails, string filename)
        {
            if (gvdetails.Rows[0].Cells[0].Text == "")
                gvdetails.Columns[0].Visible = false;
            HttpContext.Current.Response.ContentType = "application/pdf";
            HttpContext.Current.Response.AddHeader("content-disposition", "attachment;filename=" + filename + ".pdf");
            StringWriter sw = new StringWriter();
            HtmlTextWriter hw = new HtmlTextWriter(sw);
            gvdetails.AllowPaging = false;
            gvdetails.RenderControl(hw);
            gvdetails.HeaderRow.Style.Add("width", "15%");
            gvdetails.HeaderRow.Style.Add("font-size", "10px");
            gvdetails.Style.Add("text-decoration", "none");
            gvdetails.Style.Add("font-family", "Arial, Helvetica, sans-serif;");
            gvdetails.Style.Add("font-size", "8px");
            HttpContext.Current.Response.Cache.SetCacheability(HttpCacheability.NoCache);
            StringReader sr = new StringReader(sw.ToString());
            Document pdfDoc = new Document(PageSize.A2, 7f, 7f, 7f, 0f);
            HTMLWorker htmlparser = new HTMLWorker(pdfDoc);
            PdfWriter.GetInstance(pdfDoc, HttpContext.Current.Response.OutputStream);
            pdfDoc.Open();
            htmlparser.Parse(sr);
            pdfDoc.Close();
            HttpContext.Current.Response.Write(pdfDoc);
            HttpContext.Current.Response.End();
        }
    }
}


Copyright (c) 2012, Apolonio, Isagani JR. All rights reserved.  Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:  

1. Redistributions of source code must retain the above copyright notice, this  list of conditions and the following disclaimer.  
2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.   

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.  
The views and conclusions contained in the software and documentation are those of the authors and should not be interpreted as representing official policies,  either expressed or implied, of the FreeBSD Project.