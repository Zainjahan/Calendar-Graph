<template>
  <div id="dat1"></div>
</template>
<script>
import * as d3 from "d3";
export default {
  mounted() {
    const headers = { "Content-Type": "application/json" };
    fetch("z.json", { headers })
      .then((response) => response.json())
      .then((data) => {
        console.log(data);
        const margin = { top: 100, bottom: 100, left: 100, right: 100 },
          width = 1520 - margin.left - margin.right,
          height = 600 - margin.top - margin.bottom;
          

      let companies=[];
      data.forEach((d)=>{
        companies.push(d.company);
      });
     
    

        let roles=[];
        data.forEach((d)=>{
          d.roles.forEach((d1)=>{
            roles.push(d1.role);
          });
        });
        console.log(roles);
         let months=[];
      data.forEach((d)=>{
        d.roles.forEach((d2)=>{
           d2.months.forEach((d3)=>{
             months.push(d3.month.split('-')[0]);
           }
           );
        });
      });
      

        const svgWidth = width + margin.left + margin.right;

        const svg = d3
          .select("#dat1")
          .append("svg")
          .attr("width", svgWidth)
          .attr("height", height + margin.top + margin.bottom)
          .append("g")
          .attr(
            "transform",
            "translate(" + margin.left + "," + margin.top + ")"
          );

        const x0 = d3.scaleBand().domain(companies).range([0, svgWidth]);
        svg
          .append("g")
          .attr(
            "transform",
            "translate(" + 0 + "," + 0 + ")"
          )
          .call(d3.axisBottom(x0));
 

        const y1 = d3.scaleBand().domain(roles).range([0, height]);

        svg
          .append("g")
          .attr(
            "transform",
            "translate(" + 0+ "," + 0 + ")"
          )
          .call(d3.axisLeft(y1));

 const x1 = d3.scaleBand().domain(months).range([0, x0.bandwidth()]);
             

        
        svg
          .selectAll(".company")
          .data(data)
          .enter()
          .append("rect")
          .attr("class", "company")
          .attr("x", (d) => {
            console.log(d.company);
            return x0(d.company);
          })
          .attr("y", 50)
        .attr("transform",(d,i)=>{
            const x=i*30;
            return "translate("+x+",0)";
        })
          .attr("width", x0.bandwidth())
          .attr("height", 150)
          .attr("fill", "#0000ff");
        companies.forEach(c=>{
          svg
          .append("g")
          .attr(
            "transform",
            "translate(" +x0(c)+ "," + height + ")"
          )
          .call(d3.axisBottom(x1));
          
          

          months.forEach(m=>{
            roles.forEach(r=>{
              svg
              .append("circle")
              .attr("cx", x0(c)+x1(m)+x1.bandwidth()/2)
              .attr("cy", y1(r)+y1.bandwidth()/2)
              .attr("r", "10")
              .style("fill", "red");
    
              });
            })
            
        });
        
        
function make_x_gridlines() {		
    return d3.axisBottom(x0)
        .ticks(15)
        
}


function make_y_gridlines() {		
    return d3.axisLeft(y1)
        .ticks(15)
}

 svg.append("g")			
      .attr("class", "grid")
      .attr("transform", "translate(0," + height + ")")
      .call(make_x_gridlines()
          .tickSize(-height)
          .tickFormat("")
      )

  
  svg.append("g")			
      .attr("class", "grid")
      .call(make_y_gridlines()
          .tickSize(-width)
          .tickFormat("")
      )
      });
  },
};
</script>
<style scoped>
.grid{
  color: #dde0eb;
}
</style>